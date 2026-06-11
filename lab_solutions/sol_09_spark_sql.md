# Soluzioni Lab 09 — Spark SQL: query e viste temporanee

## Cella 1 — Java (Windows)

## Setup

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, to_date, year

spark = SparkSession.builder.appName("Sol09").master("local[*]").getOrCreate()

df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
df = df.withColumn("order_year", year(to_date(col("`Order Date`"), "dd/MM/yyyy")))
```

## Step 1 — Registra come vista

```python
df.createOrReplaceTempView("sales")
```

## Step 2 — Query base

```python
q2 = spark.sql("""
    SELECT Region, Category,
           ROUND(SUM(try_cast(Sales AS DOUBLE)), 2) AS total_sales,
           COUNT(*) AS num_orders
    FROM sales
    WHERE order_year = 2017
    GROUP BY Region, Category
    ORDER BY total_sales DESC
""")
q2.show()
```

## Step 3 — HAVING

```python
q3 = spark.sql("""
    SELECT `Customer Name`, ROUND(SUM(try_cast(Sales AS DOUBLE)), 2) AS total_spent
    FROM sales
    GROUP BY `Customer Name`
    HAVING SUM(Sales) > 10000
    ORDER BY total_spent DESC
""")
print(f"Clienti con spesa > 10000: {q3.count()}")
q3.show()
```

## Step 4 — Subquery

```python
q4 = spark.sql("""
    SELECT `Product Name`, Sales
    FROM sales
    WHERE Sales > (SELECT AVG(Sales) FROM sales)
    ORDER BY Sales DESC
    LIMIT 10
""")
print("Prodotti con vendite sopra la media:")
q4.show(truncate=False)
```

## Step 5 — CTE

```python
q5 = spark.sql("""
    WITH avg_sales AS (
        SELECT AVG(Sales) AS media
        FROM sales
    )
    SELECT `Product Name`, Sales
    FROM sales, avg_sales
    WHERE Sales > avg_sales.media
    ORDER BY Sales DESC
    LIMIT 10
""")
print("Stessi risultati con CTE:")
q5.show(truncate=False)
```

## Step 6 — JOIN in SQL

```python
lookup = spark.createDataFrame([
    ("Furniture", "Arredamento"),
    ("Technology", "Tecnologia"),
    ("Office Supplies", "Materiale ufficio")
], ["Category", "cat_it"])

lookup.createOrReplaceTempView("categorie")

q6 = spark.sql("""
    SELECT s.`Customer Name`, s.Category, c.cat_it, s.Sales
    FROM sales s
    JOIN categorie c ON s.Category = c.Category
    ORDER BY s.Sales DESC
    LIMIT 5
""")
q6.show()
```

## Step 7 — Window function RANK()

```python
q7 = spark.sql("""
    SELECT `Product Name`, Category, Sales,
           RANK() OVER (PARTITION BY Category ORDER BY Sales DESC) AS rank
    FROM sales
""")
# Mostra solo top 3 per categoria
q7.filter(col("rank") <= 3).orderBy("Category", "rank").show(9, truncate=False)
```

## Step 8 — Confronto DataFrame API vs SQL

```python
# SQL
sql_result = spark.sql("""
    SELECT Region, Category,
           ROUND(SUM(try_cast(Sales AS DOUBLE)), 2) AS total_sales,
           COUNT(*) AS num_orders
    FROM sales
    WHERE order_year = 2017
    GROUP BY Region, Category
    ORDER BY total_sales DESC
""")

# DataFrame API
from pyspark.sql.functions import sum as spark_sum, count, round as spark_round
api_result = (df
    .filter(col("order_year") == 2017)
    .groupBy("Region", "Category")
    .agg(
        spark_round(spark_sum("Sales"), 2).alias("total_sales"),
        count("*").alias("num_orders")
    )
    .orderBy(col("total_sales").desc())
)

print("SQL:")
sql_result.show(5)
print("DataFrame API:")
api_result.show(5)
# I risultati sono identici!
```

## Cleanup

```python
spark.stop()
```
