# Soluzioni Lab 13 — Batch Pipeline Practice: CSV → PySpark → Parquet

Questa soluzione usa il dataset **Serie A** (Opzione A). La versione Superstore segue la stessa struttura.

## Fase 1: Ingestione

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, to_date, year, month, count, sum, avg
from pyspark.sql.functions import round as spark_round, when, desc
from pyspark.sql.functions import sum as spark_sum

spark = SparkSession.builder.appName("Sol13_Pipeline").getOrCreate()

df_raw = spark.read.csv("serie_a_coppa_italia_2015_2023.csv", header=True, inferSchema=True)
print(f"Righe raw: {df_raw.count()}")        # ~3788
print(f"Colonne: {len(df_raw.columns)}")      # ~228
df_raw.printSchema()
```

### Conta i null

```python
df_raw.show(5)

null_counts = df_raw.select([
    spark_sum(col(c).isNull().cast("int")).alias(c)
    for c in df_raw.columns
])
null_counts.show()
```

## Fase 2: Pulizia

```python
cols = ["Competition_Name", "Season_End_Year", "Date", "Home", "Away",
        "HomeGoals", "AwayGoals", "Referee"]
df_selected = df_raw.select(cols)

df_clean = (df_selected
    .withColumn("match_date", to_date(col("Date"), "M/d/yyyy"))
    .drop("Date")
    .dropna(subset=["Home", "Away", "HomeGoals", "AwayGoals"])
    .dropDuplicates()
)
print(f"Righe pulite: {df_clean.count()}")
```

## Fase 3: Trasformazione

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

## Fase 4: Analisi con Spark SQL

```python
df_transformed.createOrReplaceTempView("pipeline_data")
```

### Query 1 — Aggregazione per anno

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

### Query 2 — Top 5 squadre per vittorie in casa

```python
q2 = spark.sql("""
    SELECT Home AS squadra, COUNT(*) AS vittorie
    FROM pipeline_data
    WHERE result = 'home_win'
    GROUP BY Home
    ORDER BY vittorie DESC
    LIMIT 5
""")
print("Query 2: Top 5 squadre per vittorie in casa")
q2.show()
```

### Query 3 — Window Function RANK

```python
q3 = spark.sql("""
    SELECT squadra, year, vittorie, rank_pos
    FROM (
        SELECT Home AS squadra, year,
               COUNT(*) AS vittorie,
               RANK() OVER (PARTITION BY year ORDER BY COUNT(*) DESC) AS rank_pos
        FROM pipeline_data
        WHERE result = 'home_win' AND Competition_Name = 'Serie A'
        GROUP BY Home, year
    )
    WHERE rank_pos <= 3
    ORDER BY year, rank_pos
""")
print("Query 3: Top 3 squadre per vittorie per anno")
q3.show(30)
```

## Fase 5: Salvataggio in Parquet

```python
df_transformed.write.parquet("output_pipeline/full_data",
    mode="overwrite", partitionBy="year")

q1.write.parquet("output_pipeline/yearly_summary", mode="overwrite")
print("Parquet scritti!")
```

### Confronta dimensioni

```python
import os

def folder_size_mb(path):
    total = 0
    for dirpath, _, filenames in os.walk(path):
        for f in filenames:
            total += os.path.getsize(os.path.join(dirpath, f))
    return total / (1024 * 1024)

csv_size = os.path.getsize("serie_a_coppa_italia_2015_2023.csv") / (1024 * 1024)
parquet_size = folder_size_mb("output_pipeline/full_data")

print(f"CSV originale: {csv_size:.2f} MB")
print(f"Parquet output: {parquet_size:.2f} MB")
print(f"Riduzione: {(1 - parquet_size/csv_size)*100:.0f}%")
```

**Output atteso:** Parquet è ~60-80% più piccolo del CSV.

### Rileggi e verifica

```python
df_check = spark.read.parquet("output_pipeline/full_data")
print(f"Parquet rows: {df_check.count()}")
df_check.show(3)
```

## Cleanup

```python
import shutil
shutil.rmtree("output_pipeline", ignore_errors=True)
spark.stop()
```