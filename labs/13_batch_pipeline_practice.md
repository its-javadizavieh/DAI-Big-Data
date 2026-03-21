# Lab 13 - Batch Pipeline Practice: CSV → PySpark → Parquet

## Obiettivo

Costruire una pipeline batch completa sul tuo dataset: caricamento, pulizia, trasformazione, analisi con Spark SQL, salvataggio in Parquet.

## Durata

30 minuti

## Prerequisiti

- Lab 05-12 completati
- Dataset scelto: `serie_a_coppa_italia_2015_2023.csv` **oppure** `superstore_sales.csv`

---

## Step

### Fase 1: Ingestione (5 minuti)

1. **Crea un nuovo notebook** `lab_13_pipeline.ipynb`

2. **Carica il tuo dataset:**

   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import col, to_date, year, month, count, sum, avg
   from pyspark.sql.functions import round as spark_round, when, desc

   spark = SparkSession.builder.appName("Lab13_Pipeline").getOrCreate()

   # === SCEGLI IL TUO DATASET ===
   # Opzione A: Serie A
   df_raw = spark.read.csv("serie_a_coppa_italia_2015_2023.csv", header=True, inferSchema=True)

   # Opzione B: Superstore
   # df_raw = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)

   print(f"Righe raw: {df_raw.count()}")
   print(f"Colonne: {len(df_raw.columns)}")
   df_raw.printSchema()
   ```

3. **Mostra le prime righe e conta i null:**

   ```python
   df_raw.show(5)

   # Conta null per colonna
   from pyspark.sql.functions import col, sum as spark_sum
   null_counts = df_raw.select([
       spark_sum(col(c).isNull().cast("int")).alias(c)
       for c in df_raw.columns
   ])
   null_counts.show()
   ```

### Fase 2: Pulizia (5 minuti)

4. **Seleziona le colonne utili e pulisci:**

   **Serie A:**
   ```python
   cols = ["Competition_Name", "Season_End_Year", "Date", "Home", "Away",
           "HomeGoals", "AwayGoals", "Referee"]
   df_selected = df_raw.select(cols)

   # Fix date + remove nulls
   df_clean = (df_selected
       .withColumn("match_date", to_date(col("Date"), "M/d/yyyy"))
       .drop("Date")
       .dropna(subset=["Home", "Away", "HomeGoals", "AwayGoals"])
       .dropDuplicates()
   )
   print(f"Righe pulite: {df_clean.count()}")
   ```

   **Superstore:**
   ```python
   cols = ["Order Date", "Region", "Category", "Sub-Category", "Product Name", "Sales"]
   df_selected = df_raw.select([col(c) for c in cols])

   df_clean = (df_selected
       .withColumn("order_date", to_date(col("`Order Date`"), "dd/MM/yyyy"))
       .drop("Order Date")
       .dropna(subset=["Sales", "Category"])
       .dropDuplicates()
   )
   print(f"Righe pulite: {df_clean.count()}")
   ```

### Fase 3: Trasformazione (5 minuti)

5. **Aggiungi colonne derivate:**

   **Serie A:**
   ```python
   df_transformed = (df_clean
       .withColumn("total_goals", col("HomeGoals") + col("AwayGoals"))
       .withColumn("result",
           when(col("HomeGoals") > col("AwayGoals"), "home_win")
           .when(col("HomeGoals") < col("AwayGoals"), "away_win")
           .otherwise("draw"))
       .withColumn("year", year(col("match_date")))
       .withColumn("month", month(col("match_date")))
   )
   df_transformed.show(5)
   ```

   **Superstore:**
   ```python
   df_transformed = (df_clean
       .withColumn("year", year(col("order_date")))
       .withColumn("month", month(col("order_date")))
       .withColumn("sales_level",
           when(col("Sales") > 1000, "high")
           .when(col("Sales") > 100, "medium")
           .otherwise("low"))
   )
   df_transformed.show(5)
   ```

### Fase 4: Analisi con Spark SQL (10 minuti)

6. **Registra una vista temporanea e scrivi 3 query:**

   ```python
   df_transformed.createOrReplaceTempView("pipeline_data")
   ```

   **Query 1 - Aggregazione per anno:**

   **Serie A:**
   ```python
   q1 = spark.sql("""
       SELECT year, COUNT(*) AS matches,
              ROUND(AVG(total_goals), 2) AS avg_goals
       FROM pipeline_data
       GROUP BY year
       ORDER BY year
   """)
   print("Query 1: Media gol per anno")
   q1.show()
   ```

   **Superstore:**
   ```python
   q1 = spark.sql("""
       SELECT year, COUNT(*) AS orders,
              ROUND(SUM(Sales), 2) AS total_sales
       FROM pipeline_data
       GROUP BY year
       ORDER BY year
   """)
   print("Query 1: Vendite totali per anno")
   q1.show()
   ```

   **Query 2 - Top 5:** scrivi una query che trovi i top 5 (squadre per vittorie / sotto-categorie per vendite).

   **Query 3 - HAVING o Window Function:** scrivi una query avanzata con HAVING o RANK().

### Fase 5: Salvataggio in Parquet (5 minuti)

7. **Salva i risultati in Parquet:**

   ```python
   # Salva dataset completo partizionato per anno
   df_transformed.write.parquet("output_pipeline/full_data",
       mode="overwrite", partitionBy="year")

   # Salva risultato Query 1
   q1.write.parquet("output_pipeline/yearly_summary", mode="overwrite")

   print("Parquet scritti!")
   ```

8. **Confronta le dimensioni:**

   ```python
   import os

   def folder_size_mb(path):
       total = 0
       for dirpath, _, filenames in os.walk(path):
           for f in filenames:
               total += os.path.getsize(os.path.join(dirpath, f))
       return total / (1024 * 1024)

   csv_size = os.path.getsize("serie_a_coppa_italia_2015_2023.csv") / (1024*1024)
   # oppure: os.path.getsize("superstore_sales.csv") / (1024*1024)
   parquet_size = folder_size_mb("output_pipeline/full_data")

   print(f"CSV originale: {csv_size:.2f} MB")
   print(f"Parquet output: {parquet_size:.2f} MB")
   print(f"Riduzione: {(1 - parquet_size/csv_size)*100:.0f}%")
   ```

9. **Rileggi il Parquet e verifica:**

   ```python
   df_check = spark.read.parquet("output_pipeline/full_data")
   print(f"Parquet rows: {df_check.count()}")
   df_check.show(3)
   ```

10. **Cleanup e chiudi:**

    ```python
    import shutil
    shutil.rmtree("output_pipeline", ignore_errors=True)
    spark.stop()
    ```

---

## Checklist finale

- [ ] Dataset caricato e schema verificato
- [ ] Null contati e rimossi
- [ ] Colonne derivate aggiunte (almeno 2)
- [ ] 3 query Spark SQL che funzionano
- [ ] Parquet scritto e confrontato con CSV
- [ ] Notebook completo e funzionante
