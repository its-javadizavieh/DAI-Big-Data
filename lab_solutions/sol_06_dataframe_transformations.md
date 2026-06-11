# Soluzioni Lab 06 — PySpark DataFrame API: trasformazioni

## Cella 1 — Java (Windows)

## Setup — Carica il dataset

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, round as spark_round, when, upper

spark = SparkSession.builder.appName("Lab06_Transformations").master("local[*]").getOrCreate()

# Carica il dataset Superstore Sales
# quote/escape necessari: Product Name contiene virgole e virgolette (es. 14 7/8" x 11")
df = (
    spark.read
    .option("quote", '"')
    .option("escape", '"')
    .option("multiLine", True)
    .csv("superstore_sales.csv", header=True, inferSchema=True)
)
df.show(5)
print(f"Righe: {df.count()}, Colonne: {len(df.columns)}")
```

## 1. select()

```python
# Solo Customer Name e Sales
df.select("Customer Name", "Sales").show(5)

# Prova: Customer Name, City, Category
df.select("Customer Name", "City", "Category").show(5)
```

## 2. filter()

```python
# Ordini con Sales > 500
grandi_ordini = df.filter(col("Sales") > 500)
grandi_ordini.show(5)
print(f"Ordini con Sales > 500: {grandi_ordini.count()}")

# Filtro doppio: Technology con Sales > 100
df.filter((col("Category") == "Technology") & (col("Sales") > 100)).show(5)
```

## 3. withColumn() — Sales con tassa

```python
# Vendite con tassa del 10%
df2 = df.withColumn(
    "Sales_With_Tax",
    spark_round(col("Sales") * 1.10, 2)
)
df2.select("Customer Name", "Sales", "Sales_With_Tax").show(5)

# Colonna High_Value
df3 = df2.withColumn(
    "High_Value",
    when(col("Sales") >= 100, True).otherwise(False)
)
df3.select("Customer Name", "Sales", "High_Value").show(5)
```

## 4. withColumn() testo + withColumnRenamed()

```python
# Città in maiuscolo
df.withColumn("City_Upper", upper(col("City"))) \
  .select("City", "City_Upper").show(5)

# Rinomina colonne
df_rin = (df
    .withColumnRenamed("Customer Name", "Customer")
    .withColumnRenamed("Sub-Category", "SubCategory")
)
df_rin.select("Customer", "SubCategory", "Sales").show(5)
```

## 5. drop() e orderBy()

```python
# Rimuovi colonna Row ID
df.drop("Row ID").printSchema()

# Ordina per Sales decrescente
df.orderBy(col("Sales").desc()).show(5)

# Ordina per Category (A-Z) poi Sales (desc)
df.orderBy("Category", col("Sales").desc()).show(5)
```

## 6. Chain completa

```python
report = (
    df
    .withColumn("Sales_With_Tax", spark_round(col("Sales") * 1.10, 2))
    .filter(col("Sales") >= 100)
    .select("Customer Name", "City", "Category", "Sales", "Sales_With_Tax")
    .withColumnRenamed("Customer Name", "Customer")
    .orderBy(col("Sales").desc())
)
print("Report finale (Sales >= 100, ordinato):")
report.show(10)
```

## 7. Immutabilità dei DataFrame

```python
# Questo NON modifica df!
df.withColumn("Discount", col("Sales") * 0.1)
print(f"Colonne df originale: {df.columns}")
# 'Discount' NON appare — perché withColumn() crea un NUOVO DataFrame

# Per tenere il risultato:
df_disc = df.withColumn("Discount", col("Sales") * 0.1)
print(f"Colonne df_disc: {df_disc.columns}")
# ORA 'Discount' c'è!
```

## Cleanup

```python
spark.stop()
```
