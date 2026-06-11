# Soluzioni Lab 07 — PySpark azioni e lazy evaluation

## Cella 1 — Java (Windows)

## Setup

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, when, round as spark_round
import time

spark = SparkSession.builder.appName("Lab07_Actions").master("local[*]").getOrCreate()
```

## 1. Carica CSV

```python
# Usa il tuo dataset scelto (Superstore o Serie A)
df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
print(f"Righe: {df.count()}, Colonne: {len(df.columns)}")
df.show(5)
```

## 2. Catena di trasformazioni (LAZY — non eseguita!)

```python
df_transformed = (
    df
    .filter(col("Sales").try_cast("double") > 100)
    .select("Region", "Category", "Sales")
    .withColumn("Sales_EUR", spark_round(col("Sales") * 0.92, 2))
    .withColumn("fascia", when(col("Sales") > 500, "Alta")
                             .when(col("Sales") > 200, "Media")
                             .otherwise("Bassa"))
)
# Fino a qui Spark NON ha fatto nulla! Ha solo registrato il piano.
```

## 3. Azioni — ORA Spark lavora

```python
# show() — AZIONE: forza l'esecuzione
print("Primi 5 risultati:")
df_transformed.show(5)

# count() — AZIONE: conta le righe
print(f"Righe filtrate: {df_transformed.count()}")

# first() — AZIONE: prende la prima riga
print(f"Prima riga: {df_transformed.first()}")

# describe() — AZIONE: statistiche
print("Statistiche:")
df_transformed.describe().show()
```

## 4. explain() — mostra il piano di esecuzione

```python
print("=== PIANO DI ESECUZIONE ===")
df_transformed.explain(True)

# Annotazioni:
# - 'Filter (isnotnull(Sales) AND (Sales > 100))' → il filtro è applicato PRIMA
# - 'Project [Region, Category, Sales, ...]' → solo colonne necessarie
# - Spark ha ottimizzato: legge solo le colonne richieste dal CSV
# - Non c'è shuffle (nessun groupBy/join)
```

## 5. Test caching

```python
df_filtered = df.filter(col("Sales").try_cast("double") > 100).cache()

# Prima esecuzione (LENTA — carica e calcola)
start = time.time()
count1 = df_filtered.count()
time1 = time.time() - start
print(f"Prima esecuzione: {count1} righe in {time1:.3f}s")

# Seconda esecuzione (VELOCE — dati già in RAM)
start = time.time()
count2 = df_filtered.count()
time2 = time.time() - start
print(f"Seconda esecuzione: {count2} righe in {time2:.3f}s")

if time2 < time1:
    print(f"Speedup: {time1/time2:.1f}x più veloce!")
else:
    print("Su dataset piccoli la differenza può essere minima — è normale")
```

## 6. Rilascia cache e chiudi

```python
df_filtered.unpersist()
print("Cache rilasciata.")

spark.stop()
print("SparkSession chiusa.")
```

## Annotazioni su explain()

L'output di `explain(True)` mostra 4 piani:

1. **Parsed Logical Plan** — cosa hai scritto
2. **Analyzed Logical Plan** — con tipi risolti
3. **Optimized Logical Plan** — dopo le ottimizzazioni del Catalyst
4. **Physical Plan** — come Spark lo eseguirà davvero

Cose da notare:

- Il **filtro** (`Sales > 100`) viene applicato il prima possibile (predicate pushdown)
- Solo le **colonne necessarie** vengono lette dal CSV (column pruning)
