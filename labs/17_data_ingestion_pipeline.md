# Lab 17 - Pipeline di ingestion: CSV -> PySpark -> TinyDB

## Obiettivo

Costruire una pipeline ETL semplice: caricare il CSV Superstore con PySpark, pulire i dati, e salvarli in TinyDB.

## Durata

30 minuti

## Prerequisiti

- Lab 10 completato
- Lab 16 completato
- `pip install tinydb`

## Dataset

**superstore_sales.csv**

## Step

### Fase 1: Extract (5 minuti)

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Lab17_TinyDB").getOrCreate()
df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)

print(f"Righe caricate: {df.count()}")
df.printSchema()
```

### Fase 2: Validate (5 minuti)

```python
from pyspark.sql.functions import col, count, when

null_counts = df.select([
    count(when(col(c).isNull(), c)).alias(c)
    for c in df.columns
])
null_counts.show()
```

### Fase 3: Clean (8 minuti)

```python
from pyspark.sql.functions import to_date, year, month

clean_df = (
    df
    .dropDuplicates()
    .dropna(subset=["Order ID", "Sales"])
    .withColumn("order_date", to_date(col("Order Date"), "dd/MM/yyyy"))
    .withColumn("order_year", year(col("order_date")))
    .withColumn("order_month", month(col("order_date")))
)
```

### Fase 4: Load (8 minuti)

```python
from tinydb import TinyDB

to_store = clean_df.select(
    col("Order ID").alias("order_id"),
    col("Customer Name").alias("customer_name"),
    col("Region").alias("region"),
    col("Category").alias("category"),
    col("Sub-Category").alias("sub_category"),
    col("Sales").alias("sales"),
    col("order_year"),
    col("order_month")
)

db = TinyDB("superstore_tinydb.json")
orders = db.table("orders")
orders.truncate()
orders.insert_multiple([row.asDict() for row in to_store.collect()])
```

### Fase 5: Verifica (4 minuti)

```python
print(f"Spark: {to_store.count()} righe")
print(f"TinyDB: {len(orders)} documenti")
print(orders.all()[:3])
```

## Output atteso

- CSV caricato correttamente
- Duplicati rimossi
- Colonne anno e mese create
- Documenti salvati in TinyDB
- Conteggi Spark e TinyDB uguali

## Checkpoint

- [ ] CSV letto con schema corretto
- [ ] Null controllati
- [ ] Dati puliti
- [ ] TinyDB popolato
- [ ] Conteggi verificati
