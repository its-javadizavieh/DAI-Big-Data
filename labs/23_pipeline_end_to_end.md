# Lab 23 - Pipeline end-to-end: CSV -> PySpark -> TinyDB

## Obiettivo

Costruire una pipeline completa che carica un CSV, lo pulisce, lo analizza con Spark SQL, e salva i risultati finali in TinyDB.

## Durata

30 minuti

## Prerequisiti

- Lab 17 completato
- Lab 19 completato
- TinyDB installato

## Dataset

**superstore_sales.csv**

## Step

### Fase 1: Ingestione (5 minuti)

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, to_date, year

spark = SparkSession.builder.appName("Lab23_Pipeline").getOrCreate()
raw = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
print(f"Righe caricate: {raw.count()}")
```

### Fase 2: Pulizia (5 minuti)

```python
clean = (
    raw
    .dropDuplicates()
    .dropna(subset=["Order ID", "Sales"])
    .withColumn("order_date", to_date(col("Order Date"), "dd/MM/yyyy"))
    .withColumn("order_year", year(col("order_date")))
)
```

### Fase 3: Analisi con Spark SQL (10 minuti)

```python
clean.createOrReplaceTempView("orders")

q1 = spark.sql("""
    SELECT Region, order_year,
           ROUND(SUM(Sales), 2) AS total_sales,
           COUNT(*) AS num_orders
    FROM orders
    GROUP BY Region, order_year
    ORDER BY Region, order_year
""")

q2 = spark.sql("""
    SELECT `Customer Name` AS customer,
           ROUND(SUM(Sales), 2) AS total_spent
    FROM orders
    GROUP BY `Customer Name`
    ORDER BY total_spent DESC
    LIMIT 5
""")

q1.show()
q2.show()
```

### Fase 4: Salva i risultati (5 minuti)

```python
from tinydb import TinyDB

db = TinyDB("pipeline_results.json")
region_table = db.table("sales_by_region")
customer_table = db.table("top_customers")

region_table.truncate()
customer_table.truncate()

region_table.insert_multiple([row.asDict() for row in q1.collect()])
customer_table.insert_multiple([row.asDict() for row in q2.collect()])
```

### Fase 5: Verifica (5 minuti)

```python
print(f"q1 Spark rows: {q1.count()} | TinyDB docs: {len(region_table)}")
print(f"q2 Spark rows: {q2.count()} | TinyDB docs: {len(customer_table)}")
print(region_table.all()[:3])
print(customer_table.all())
```

## Output atteso

- Dati caricati e puliti
- 2 query Spark SQL funzionanti
- 2 tabelle TinyDB create
- Conteggi uguali tra Spark e TinyDB

## Checkpoint

- [ ] CSV caricato
- [ ] Data cleaning completato
- [ ] 2 query eseguite
- [ ] TinyDB popolato
- [ ] Verifica completata
