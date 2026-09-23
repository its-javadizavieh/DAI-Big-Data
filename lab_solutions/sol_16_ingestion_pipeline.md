# Soluzioni Lab 16 — Pipeline di ingestion: CSV -> PySpark -> TinyDB

## Extract

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Lab16TinyDB").getOrCreate()
df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)

print(f"Righe caricate: {df.count()}")
df.printSchema()
```

## Validate

```python
from pyspark.sql.functions import col, count, when

null_counts = df.select([
    count(when(col(c).isNull(), c)).alias(c)
    for c in df.columns
])
null_counts.show()
```

## Clean

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

## Load

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

## Verifica

```python
print(f"Spark: {to_store.count()} righe")
print(f"TinyDB: {len(orders)} documenti")
print(orders.all()[:3])
```