# Lab 24 — Mini-progetto: progettazione e avvio

## Obiettivo

Progettare una pipeline Big Data completa su un dataset reale e avviarne l'implementazione.

## Durata

30 minuti

## Prerequisiti

- Tutti i lab precedenti completati
- Dataset a scelta: `serie_a_coppa_italia_2015_2023.csv` **oppure** `superstore_sales.csv`

---

## Step

### Fase 1: Scegli il dataset e il tema (5 minuti)

1. **Scegli UNO dei due dataset e UNO dei temi suggeriti:**

   **Opzione A — Serie A (calcio)**
   - Tema 1: "Analisi performance delle squadre top 6 nelle ultime 5 stagioni"
   - Tema 2: "Fattore campo: come cambia il vantaggio di giocare in casa"
   - Tema 3: "Statistiche arbitri: chi dà più cartellini?"

   **Opzione B — Superstore (vendite)**
   - Tema 1: "Report vendite per regione: trend e top clienti"
   - Tema 2: "Analisi categorie prodotti: cosa vende di più e dove"
   - Tema 3: "Segmentazione clienti: Consumer vs Corporate vs Home Office"

2. **Scrivi nel tuo notebook:**
   - Dataset scelto: \_\_\_\_
   - Tema scelto: \_\_\_\_
   - 3 domande a cui vuoi rispondere con i dati

### Fase 2: Documento di progettazione (10 minuti)

3. **Compila questo template nel tuo notebook (cella Markdown):**

   ```markdown
   # Mini-Progetto: [TITOLO]

   ## Dataset

   - Nome file: \_\_\_\_
   - Righe: \_\_\_\_
   - Colonne principali usate: \_\_\_\_

   ## Domande di analisi

   1. ***
   2. ***
   3. ***

   ## Pipeline prevista

   1. **Ingestione**: caricamento CSV con PySpark
   2. **Pulizia**: [descrivi cosa devi pulire]
   3. **Analisi**: [descrivi le query/aggregazioni]
   4. **Storage**: salvataggio risultati in MongoDB
   5. **Presentazione**: tabelle riepilogative

   ## Colonne necessarie

   [lista delle colonne che userai]
   ```

### Fase 3: Avvia l'implementazione (15 minuti)

4. **Step 1 — Ingestione e pulizia:**

   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import col, to_date, year, count, sum, avg, when
   from pyspark.sql.functions import round as spark_round

   spark = SparkSession.builder.appName("MiniProject").getOrCreate()

   # Carica il TUO dataset scelto
   df = spark.read.csv("NOME_FILE.csv", header=True, inferSchema=True)
   print(f"Righe: {df.count()}, Colonne: {len(df.columns)}")
   ```

5. **Step 2 — Seleziona e pulisci:**

   ```python
   # Seleziona le colonne che ti servono
   cols = ["..."]  # <-- COMPLETA TU
   selected = df.select(cols)

   # Rimuovi null e duplicati
   clean = selected.dropna().dropDuplicates()
   print(f"Righe pulite: {clean.count()}")
   ```

6. **Step 3 — Prima query (rispondi alla domanda 1):**

   ```python
   clean.createOrReplaceTempView("project_data")

   # Scrivi la query per rispondere alla tua prima domanda
   q1 = spark.sql("""
       SELECT ...
       FROM project_data
       ...
   """)
   q1.show()
   ```

7. **Salva il notebook.** Continueremo nel Lab 25.

## Output atteso

- Dataset e tema scelti
- Documento di progettazione compilato
- CSV caricato e pulito
- Prima query funzionante

## Checkpoint

- [ ] Dataset e tema scelti
- [ ] 3 domande di analisi scritte
- [ ] Template di progettazione compilato
- [ ] Ingestione completata (count verificato)
- [ ] Pulizia completata
- [ ] Prima query eseguita con risultati

## Suggerimenti

- Non scegliere troppe colonne — 8-12 sono sufficienti
- Le domande devono essere specifiche: "Quale regione..." è meglio di "Analizza le vendite"
- Puoi combinare query semplici (filtri, group by) con una più avanzata (window function)

## Cleanup

**NON fare cleanup!** Continueremo nel Lab 25.
