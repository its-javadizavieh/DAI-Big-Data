# Soluzioni Lab 05 — Setup ambiente: Python + Jupyter + PySpark

## Verifica installazione

Apri il terminale in VS Code (Ctrl+`) ed esegui:

```bash
python -m venv bigdata_env
bigdata_env\Scripts\activate      # su Windows
pip install pyspark pandas matplotlib seaborn numpy
```

Poi crea un nuovo file `lab_05_setup.ipynb` in VS Code e seleziona il kernel dal virtual environment.

## Cella 1: Java (Windows)

## Cella 2: SparkSession

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("Lab05") \
    .master("local[*]") \
    .getOrCreate()

print(f"Spark version: {spark.version}")
# Output atteso: 3.x.x
```

## Cella 3: DataFrame da lista Python

```python
data = [
    (1, "Office Supplies", "Binders", 14.62),
    (2, "Technology", "Phones", 907.15),
    (3, "Furniture", "Chairs", 498.00),
    (4, "Office Supplies", "Paper", 15.55),
    (5, "Technology", "Accessories", 127.98),
]

df = spark.createDataFrame(data, ["Row_ID", "Category", "Sub_Category", "Sales"])
df.show()
df.printSchema()
```

**Output atteso:**

```
+------+---------------+------------+------+
|Row_ID|       Category|Sub_Category| Sales|
+------+---------------+------------+------+
|     1|Office Supplies|     Binders| 14.62|
|     2|     Technology|      Phones|907.15|
|     3|      Furniture|      Chairs| 498.0|
|     4|Office Supplies|       Paper| 15.55|
|     5|     Technology| Accessories|127.98|
+------+---------------+------------+------+
```

## Cella 4: Leggi dataset del corso

```python
df_csv = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
df_csv.show(5)
df_csv.printSchema()
```

## Cella 5: Conta righe

```python
print(f"Numero righe: {df_csv.count()}")
# Output atteso: 9800
```

## Cella 6: Chiudi Spark

```python
spark.stop()
print("SparkSession chiusa.")
```
