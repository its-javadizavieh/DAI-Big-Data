# Soluzioni Lab 10 — Formati file: CSV, JSON, Parquet

## Cella 1 — Java (Windows)

## Setup — SparkSession e caricamento CSV

```python
from pyspark.sql import SparkSession
import time, os, shutil

spark = SparkSession.builder.appName("Sol10").master("local[*]").getOrCreate()

df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
df.printSchema()
print(f"Righe: {df.count()}")
```

## Step 2 — Scrivi in JSON

```python
df.write.json("output_json", mode="overwrite")
```

## Step 3 — Scrivi in Parquet

```python
df.write.parquet("output_parquet", mode="overwrite")
```

## Step 4 — Confronta dimensioni

```python
def folder_size(path):
    total = 0
    for dirpath, _, filenames in os.walk(path):
        for f in filenames:
            total += os.path.getsize(os.path.join(dirpath, f))
    return total / (1024 * 1024)  # MB

csv_size = os.path.getsize("superstore_sales.csv") / (1024 * 1024)
json_size = folder_size("output_json")
parquet_size = folder_size("output_parquet")

print(f"CSV:     {csv_size:.2f} MB")
print(f"JSON:    {json_size:.2f} MB")
print(f"Parquet: {parquet_size:.2f} MB")
```

**Output atteso** (approssimativo):

```
CSV:     ~1.6 MB
JSON:    ~5.0 MB   (più grande — include nomi colonne per ogni riga)
Parquet: ~0.4 MB   (molto più piccolo — compressione colonnare)
```

## Step 5 — Confronta tempi di lettura

```python
# CSV
start = time.time()
spark.read.csv("superstore_sales.csv", header=True, inferSchema=True).count()
csv_time = time.time() - start

# JSON
start = time.time()
spark.read.json("output_json").count()
json_time = time.time() - start

# Parquet
start = time.time()
spark.read.parquet("output_parquet").count()
parquet_time = time.time() - start

print(f"CSV:     {csv_time:.3f}s")
print(f"JSON:    {json_time:.3f}s")
print(f"Parquet: {parquet_time:.3f}s")
```

## Step 6 — Parquet partizionato

```python
df.write.partitionBy("Category").parquet("output_partitioned", mode="overwrite")

# Mostra la struttura delle cartelle
for item in os.listdir("output_partitioned"):
    print(f"  {item}")
```

**Output atteso:**

```
  Category=Furniture
  Category=Office Supplies
  Category=Technology
  _SUCCESS
```

## Step 7 — Leggi una singola partizione

```python
start = time.time()
tech = spark.read.parquet("output_partitioned/Category=Technology")
tech_time = time.time() - start
print(f"Righe Technology: {tech.count()}")
print(f"Tempo lettura singola partizione: {tech_time:.3f}s")
```

## Tabella riassuntiva

| Formato             | Dimensione (MB) | Tempo lettura (s) |
| ------------------- | --------------- | ----------------- |
| CSV                 | ~1.6            | ~0.8              |
| JSON                | ~5.0            | ~1.0              |
| Parquet             | ~0.4            | ~0.3              |
| Parquet partitioned | ~0.4            | ~0.1 (1 part.)    |

_(I tempi esatti dipendono dal sistema — Parquet sarà sempre il più veloce)_

## Cleanup

```python
for d in ["output_json", "output_parquet", "output_partitioned"]:
    shutil.rmtree(d, ignore_errors=True)
spark.stop()
```