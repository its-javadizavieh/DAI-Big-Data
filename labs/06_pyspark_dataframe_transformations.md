# Lab 06 — PySpark DataFrame API: trasformazioni

## Obiettivo

Usare select, filter, withColumn, drop e orderBy per trasformare il dataset Superstore Sales. Imparare a concatenare le operazioni.

## Durata (timebox)

30 minuti

## Prerequisiti

- Lab 05 completato (SparkSession funzionante, VS Code con Jupyter pronto)

## Scenario

Sei un data analyst in un'azienda di e-commerce. Il tuo manager vuole un report sulle vendite: quali categorie vendono di più, quali ordini superano una certa soglia, e un report pulito con tasse calcolate.

## Step

1. **Crea il notebook e carica i dati**: apri VS Code, crea `lab_06_transformations.ipynb`, e nella prima cella:

   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import col, upper, round as spark_round

   spark = SparkSession.builder.appName("Lab06").master("local[*]").getOrCreate()

   # Carica il dataset Superstore Sales
   df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
   df.show(5)
   print(f"Righe: {df.count()}, Colonne: {len(df.columns)}")
   ```

   Verifica che la tabella abbia 9.800 righe e 18 colonne.

2. **select() — Seleziona solo alcune colonne**: mostra solo il nome del cliente e le vendite.

   ```python
   df.select("Customer Name", "Sales").show(5)
   ```

   **Prova tu**: seleziona `Customer Name`, `City` e `Category`. Scrivi il codice e verifica.

3. **filter() — Filtra le righe**: trova gli ordini con vendite sopra 500.

   ```python
   grandi_ordini = df.filter(col("Sales") > 500)
   grandi_ordini.show(5)
   print(f"Ordini con Sales > 500: {grandi_ordini.count()}")
   ```

   **Prova tu** — filtro con 2 condizioni: trova ordini di `Technology` con vendite sopra 100.

   ```python
   # Completa tu:
   risultato = df.filter((col("Category") == "Technology") & (col("Sales") > 100))
   risultato.show(5)
   ```

   **Attenzione**: usa `&` (non `and`) e metti le parentesi attorno a ogni condizione!

4. **withColumn() — Aggiungi una colonna calcolata**: calcola le vendite con tassa del 10%.

   ```python
   df2 = df.withColumn(
       "Sales_With_Tax",
       spark_round(col("Sales") * 1.10, 2)
   )
   df2.select("Customer Name", "Sales", "Sales_With_Tax").show(5)
   ```

   **Output atteso**: una nuova colonna `Sales_With_Tax` con il valore maggiorato del 10%.

   **Prova tu**: aggiungi una colonna `High_Value` che è `True` se Sales >= 100, `False` altrimenti.

   ```python
   df3 = df2.withColumn("High_Value", col("Sales") >= 100)
   df3.select("Customer Name", "Sales", "High_Value").show(5)
   ```

5. **withColumn() con testo**: crea una colonna con la città in maiuscolo.

   ```python
   df4 = df.withColumn("City_Upper", upper(col("City")))
   df4.select("City", "City_Upper").show(5)
   ```

6. **withColumnRenamed() — Rinomina una colonna**:

   ```python
   df_rin = df.withColumnRenamed("Customer Name", "Customer") \
              .withColumnRenamed("Sub-Category", "SubCategory")
   df_rin.select("Customer", "SubCategory", "Sales").show(5)
   ```

7. **drop() — Elimina una colonna**: rimuovi la colonna `Row ID` (non ci serve nel report).

   ```python
   df_no_id = df.drop("Row ID")
   df_no_id.show(5)
   ```

8. **orderBy() — Ordina i dati**: ordina per vendite dal più alto al più basso.

   ```python
   df.orderBy(col("Sales").desc()).show(5)
   ```

   **Prova tu**: ordina per `Category` (A-Z) e poi per `Sales` (decrescente).

   ```python
   df.orderBy("Category", col("Sales").desc()).show(5)
   ```

9. **Chain — Concatena tutto in un'unica espressione**:

   ```python
   report = (
       df
       .withColumn("Sales_With_Tax", spark_round(col("Sales") * 1.10, 2))
       .filter(col("Sales") >= 100)
       .select("Customer Name", "City", "Category", "Sales", "Sales_With_Tax")
       .withColumnRenamed("Customer Name", "Customer")
       .orderBy(col("Sales").desc())
   )
   report.show(10)
   ```

   **Questo è il potere del chaining**: in poche righe hai calcolato la tassa, filtrato gli ordini rilevanti, selezionato le colonne utili, rinominato, e ordinato!

10. **Ricorda: i DataFrame sono immutabili!** Prova questo per capire:

    ```python
    # Questo NON modifica df!
    df.withColumn("Discount", col("Sales") * 0.1)
    df.show(5)  # La colonna "Discount" NON c'è!

    # Per tenere il risultato, salvalo in una nuova variabile:
    df_disc = df.withColumn("Discount", col("Sales") * 0.1)
    df_disc.select("Customer Name", "Sales", "Discount").show(5)  # ORA la colonna c'è!
    ```

11. **Chiudi Spark**:

    ```python
    spark.stop()
    ```

## Output atteso

- Notebook `lab_06_transformations.ipynb` con tutte le celle eseguite
- Ogni operazione (select, filter, withColumn, drop, orderBy) testata con output visibile
- La chain finale che produce un report degli ordini con Sales >= 100, ordinati per vendite

## Checkpoint

- [ ] `df.show()` mostra le prime righe del dataset con 18 colonne
- [ ] `filter()` riduce il numero di righe
- [ ] La colonna `Sales_With_Tax` è calcolata correttamente
- [ ] Il chain finale mostra solo ordini con Sales >= 100, ordinati dal più alto
- [ ] `spark.stop()` eseguito senza errori

## Troubleshooting rapido

| Errore                                          | Soluzione                                                                          |
| ----------------------------------------------- | ---------------------------------------------------------------------------------- | -------- |
| `AnalysisException: cannot resolve column name` | Controlla maiuscole/minuscole: `df.columns` ti mostra i nomi esatti                |
| `TypeError: Column is not iterable`             | Hai usato `and` invece di `&` — PySpark vuole `&` per AND e `                      | ` per OR |
| La colonna calcolata non compare                | Ricorda: `withColumn()` restituisce un NUOVO DataFrame — salvalo in una variabile! |
| `NameError: name 'col' is not defined`          | Aggiungi `from pyspark.sql.functions import col` all'inizio                        |

## Cleanup obbligatorio

```python
spark.stop()
```

## Parole chiave Google (screenshot/guide)

- "pyspark select filter withColumn example"
- "pyspark dataframe tutorial beginner"
- "pyspark.sql.functions cheat sheet"
