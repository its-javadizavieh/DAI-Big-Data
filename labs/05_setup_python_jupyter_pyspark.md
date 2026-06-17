# Lab 05 - Setup ambiente: Python + Jupyter + PySpark

## Obiettivo

Installare l'ambiente di lavoro, creare una SparkSession funzionante, creare il primo DataFrame, e leggere un file CSV.

## Durata (timebox)

30 minuti

## Prerequisiti

- PC con accesso a Internet
- Python 3.10+ già installato sulla VM
- VS Code con estensione Jupyter installata

## Scenario

Oggi configuri il tuo ambiente di lavoro per il resto del corso. Alla fine di questo lab avrai Python, Jupyter e PySpark pronti all'uso.

## Dataset del corso

Durante il corso userai **2 dataset** già inclusi nella cartella `labs/`. **Non serve scaricare nulla** - i file sono già pronti.

| Dataset                    | File                                 | Righe         | Uso principale                   |
| -------------------------- | ------------------------------------ | ------------- | -------------------------------- |
| **Serie A & Coppa Italia** | `serie_a_coppa_italia_2015_2023.csv` | 3.788 partite | Analisi esplorativa, SQL, join   |
| **Superstore Sales**       | `superstore_sales.csv`               | 9.800 ordini  | Trasformazioni, pipeline, TinyDB |

Consulta [00_datasets_description.md](https://github.com/its-javadizavieh/DAI-Big-Data/blob/main/labs/00_datasets_description.md) per la descrizione completa delle colonne.

---

## Step

1. **Crea un virtual environment e installa i pacchetti**:
   - Apri il terminale in VS Code (Ctrl+`) e digita:

   ```bash
   python -m venv bigdata_env
   bigdata_env\Scripts\activate      # su Windows
   pip install pyspark pandas matplotlib seaborn numpy
   ```

   Verifica che Python sia 3.10+:

   ```bash
   python --version
   ```

   Dovresti vedere: `Python 3.10.x` o superiore.

2. **Installa l'estensione Jupyter in VS Code**:
   - Vai su Extensions (Ctrl+Shift+X) → cerca "Jupyter" → Installa

3. **Crea un notebook**:
   - In VS Code, crea un nuovo file `lab_05_setup.ipynb`
   - VS Code lo apre automaticamente come notebook
   - Seleziona il kernel dal virtual environment (in alto a destra)

4. **Configura Java (prima cella)**: su Windows PySpark richiede `JAVA_HOME`. Copia ed esegui questa cella **prima** di importare PySpark:

   ```python
   import os
   import platform
   import subprocess
   from pathlib import Path

   def setup_java_home():
       """Imposta JAVA_HOME e aggiunge la cartella bin al PATH per PySpark."""
       if os.environ.get("JAVA_HOME"):
           java_bin = Path(os.environ["JAVA_HOME"]) / "bin"
       elif platform.system() != "Windows":
           return
       else:
           java_home = _find_jdk_on_windows()
           if not java_home:
               print(
                   "JAVA_HOME non trovato. Installa JDK 11+ (es. Eclipse Temurin) "
                   "oppure imposta JAVA_HOME nelle variabili d'ambiente di Windows."
               )
               return
           os.environ["JAVA_HOME"] = java_home
           java_bin = Path(java_home) / "bin"
       bin_path = str(java_bin)
       path = os.environ.get("PATH", "")
       if bin_path.casefold() not in path.casefold():
           os.environ["PATH"] = bin_path + os.pathsep + path
       print(f"JAVA_HOME = {os.environ['JAVA_HOME']}")

   def _find_jdk_on_windows():
       try:
           out = subprocess.check_output(
               ["where", "java"], text=True, stderr=subprocess.DEVNULL
           )
           java_exe = Path(out.strip().splitlines()[0]).resolve()
           if java_exe.name.lower() == "java.exe" and java_exe.parent.name.lower() == "bin":
               return str(java_exe.parent.parent)
       except (subprocess.CalledProcessError, FileNotFoundError, IndexError):
           pass
       for root in (
           Path(r"C:\Program Files\Java"),
           Path(r"C:\Program Files\Eclipse Adoptium"),
           Path(r"C:\Program Files\Microsoft"),
           Path(r"C:\Program Files\Amazon Corretto"),
       ):
           if not root.is_dir():
               continue
           for folder in sorted(root.iterdir(), reverse=True):
               if (folder / "bin" / "java.exe").exists():
                   return str(folder)
       return None

   setup_java_home()
   ```

   **Output atteso**: `JAVA_HOME = C:\Program Files\...`

5. **Crea la SparkSession**: in una **seconda** cella, copia e esegui (Shift+Enter):

   ```python
   from pyspark.sql import SparkSession

   spark = SparkSession.builder \
       .appName("Lab05") \
       .master("local[*]") \
       .getOrCreate()

   print(f"Spark version: {spark.version}")
   ```

   **Output atteso**: qualcosa come `Spark version: 3.5.3`

   Se vedi la versione: complimenti, Spark funziona!

6. **Crea un DataFrame da una lista Python**: in una nuova cella:

   ```python
   data = [
       (1, "Technology", "Phones", 999.99),
       (2, "Furniture", "Chairs", 450.50),
       (3, "Office Supplies", "Paper", 15.99),
       (4, "Technology", "Accessories", 175.00),
       (5, "Furniture", "Tables", 820.00)
   ]

   df = spark.createDataFrame(data, ["Row_ID", "Category", "Sub_Category", "Sales"])
   df.show()
   ```

   **Output atteso**:

   ```
   +------+---------------+------------+------+
   |Row_ID|       Category|Sub_Category| Sales|
   +------+---------------+------------+------+
   |     1|     Technology|      Phones|999.99|
   |     2|      Furniture|      Chairs| 450.5|
   |     3|Office Supplies|       Paper| 15.99|
   |     4|     Technology| Accessories| 175.0|
   |     5|      Furniture|      Tables| 820.0|
   +------+---------------+------------+------+
   ```

7. **Controlla i tipi delle colonne**: in una nuova cella:

   ```python
   df.printSchema()
   ```

   **Output atteso**:

   ```
   root
    |-- Row_ID: long (nullable = true)
    |-- Category: string (nullable = true)
    |-- Sub_Category: string (nullable = true)
    |-- Sales: double (nullable = true)
   ```

   `string` = testo, `long` = numero intero, `double` = numero decimale.

8. **Leggi un dataset reale del corso**: i file CSV sono già nella cartella `labs/`. In una nuova cella:

   ```python
   df_csv = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
   df_csv.show(5)
   df_csv.printSchema()
   ```

   **Output atteso**: una tabella con le prime 5 righe del dataset (colonne come `Row ID`, `Order ID`, `Sales`, ecc.) e lo schema con i tipi.

9. **Conta le righe**:

   ```python
   print(f"Numero di righe: {df_csv.count()}")
   ```

   **Output atteso**: `Numero di righe: 9800`

10. **Chiudi la SparkSession** (ultima cella):
   ```python
   spark.stop()
   print("SparkSession chiusa!")
   ```

## Output atteso

- Notebook `lab_05_setup.ipynb` con:
  - SparkSession creata e versione stampata
  - DataFrame da lista con `show()` e `printSchema()`
  - File CSV creato, letto, e visualizzato
  - SparkSession chiusa correttamente

## Checkpoint

- [ ] `spark.version` stampa un numero di versione
- [ ] `df.show()` mostra 5 righe con Row_ID, Category, Sub_Category, Sales
- [ ] `df_csv.show()` mostra le prime righe del dataset Superstore
- [ ] `df_csv.count()` restituisce 9800
- [ ] `spark.stop()` eseguito senza errori

## Troubleshooting rapido

| Errore                                           | Soluzione                                                  |
| ------------------------------------------------ | ---------------------------------------------------------- |
| `ModuleNotFoundError: No module named 'pyspark'` | Attiva il venv e riesegui `pip install pyspark`            |
| `FileNotFoundError` quando leggi il CSV          | Il file deve essere nella stessa cartella del notebook     |
| Spark è lento a partire                          | È normale - il primo avvio può richiedere 10-20 secondi    |
| `JAVA_HOME is not set`                           | Esegui di nuovo la **prima cella** (setup Java) prima di PySpark |
| Il kernel non vede PySpark                       | Verifica di aver selezionato il kernel dal venv in VS Code |

## Cleanup obbligatorio

- Esegui `spark.stop()` nell'ultima cella del notebook
- Salva il notebook (Ctrl+S)

## Parole chiave Google (screenshot/guide)

- "python virtual environment windows"
- "pip install pyspark"
- "vs code jupyter extension tutorial"
- "pyspark createDataFrame example"