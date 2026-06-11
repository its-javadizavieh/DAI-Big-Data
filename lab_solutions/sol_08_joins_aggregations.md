# Soluzioni Lab 08 — PySpark: join e aggregazioni

## Cella 1 — Java (Windows)

## Setup

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, count, sum, avg, max, round as spark_round

spark = SparkSession.builder.appName("Sol08").master("local[*]").getOrCreate()

df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
print(f"Righe: {df.count()}, Colonne: {len(df.columns)}")
```

## Step 2 — groupBy + count

```python
df.groupBy("Category").count().show()
```

**Output atteso:**

```
+---------------+-----+
|       Category|count|
+---------------+-----+
|      Furniture| 2121|
|Office Supplies| 6026|
|     Technology| 1847|
+---------------+-----+
```

## Step 3 — groupBy + agg multipla

```python
from pyspark.sql.functions import sum as spark_sum

df.groupBy("Category").agg(
    spark_round(spark_sum("Sales"), 2).alias("total_sales"),
    spark_round(avg("Sales"), 2).alias("avg_sales"),
    spark_round(max("Sales"), 2).alias("max_sale"),
    count("*").alias("num_orders")
).show()
```

**Output atteso:**

```
+---------------+-----------+---------+--------+----------+
|       Category|total_sales|avg_sales|max_sale|num_orders|
+---------------+-----------+---------+--------+----------+
|      Furniture|  741999.80|  349.95|18906.06|      2121|
|Office Supplies|  719047.03|  119.33| 9892.74|      6026|
|     Technology|  836154.03|  452.57|22638.48|      1847|
+---------------+-----------+---------+--------+----------+
```

_(I numeri esatti possono variare leggermente)_

## Step 4 — Pivot table

```python
from pyspark.sql.functions import to_date, year, quarter

df2 = (df
    .withColumn("order_date", to_date(col("`Order Date`"), "dd/MM/yyyy"))
    .withColumn("year", year(col("order_date")))
    .withColumn("quarter", quarter(col("order_date")))
)

df2.groupBy("year").pivot("quarter", [1, 2, 3, 4]).agg(
    spark_round(spark_sum("Sales"), 2)
).orderBy("year").show()
```

**Output atteso:** Righe = anni, colonne = Q1-Q4 con somma vendite.

## Step 5 — Crea un secondo DataFrame

```python
lookup = spark.createDataFrame([
    ("Furniture", "Mobili e arredamento"),
    ("Technology", "Tecnologia e dispositivi"),
    ("Office Supplies", "Materiale ufficio")
], ["Category", "description"])

lookup.show()
```

## Step 6 — Inner join

```python
joined = df.join(lookup, on="Category", how="inner")
joined.select("Category", "description", "Sales").show(5)
print(f"Righe dopo inner join: {joined.count()}")
# Atteso: ~9994 (tutte le 3 categorie hanno corrispondenza)
```

## Step 7 — Left anti join

```python
# Aggiungiamo una categoria inesistente al lookup per testare
lookup2 = spark.createDataFrame([
    ("Furniture", "Mobili"),
    ("Technology", "Tech"),
    # "Office Supplies" manca!
], ["Category", "description"])

orfani = df.join(lookup2, on="Category", how="left_anti")
print(f"Righe senza corrispondenza: {orfani.count()}")
orfani.select("Category").distinct().show()
# Atteso: ~6026 righe di Office Supplies
```

## Cleanup

```python
spark.stop()
```
