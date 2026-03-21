# Lab 23 - Pipeline end-to-end: CSV → PySpark → MongoDB

## Obiettivo

Costruire una pipeline completa che: carica un CSV, lo pulisce, lo analizza con Spark SQL, e salva i risultati in MongoDB.

## Durata

30 minuti

## Prerequisiti

- Lab 13, 16-17 completati
- MongoDB in esecuzione
- `pip install pymongo`
- Dataset `superstore_sales.csv` nella cartella di lavoro

## Dataset

**superstore_sales.csv** - 9.800 ordini. Pipeline completa dall'inizio alla fine.

---

## Step

### Fase 1: Ingestione (5 minuti)

1. **Carica il CSV:**

   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import col, to_date, year, round as spark_round, sum, avg, count

   spark = SparkSession.builder.appName("Lab22_Pipeline").getOrCreate()

   raw = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
   print(f"Righe caricate: {raw.count()}")
   print(f"Colonne: {len(raw.columns)}")
   ```

### Fase 2: Pulizia (5 minuti)

2. **Rimuovi duplicati e null:**

   ```python
   step1 = raw.dropDuplicates(["`Order ID`", "`Product ID`"])
   step2 = step1.dropna(subset=["`Order ID`", "Sales"])
   print(f"Dopo dedup+dropna: {step2.count()} righe")
   ```

3. **Converti le date e aggiungi anno:**

   ```python
   clean = (step2
       .withColumn("order_date", to_date(col("`Order Date`"), "dd/MM/yyyy"))
       .withColumn("ship_date", to_date(col("`Ship Date`"), "dd/MM/yyyy"))
       .withColumn("order_year", year(col("order_date")))
   )
   clean.printSchema()
   ```

### Fase 3: Analisi con Spark SQL (10 minuti)

4. **Registra come vista:**

   ```python
   clean.createOrReplaceTempView("orders")
   ```

5. **Query 1 - Vendite per regione e anno:**

   ```python
   q1 = spark.sql("""
       SELECT
           Region,
           order_year,
           ROUND(SUM(Sales), 2) AS total_sales,
           COUNT(*) AS num_orders
       FROM orders
       GROUP BY Region, order_year
       ORDER BY Region, order_year
   """)
   print("Vendite per regione e anno:")
   q1.show(20)
   ```

6. **Query 2 - Top 5 clienti:**

   ```python
   q2 = spark.sql("""
       SELECT
           `Customer Name` AS customer,
           ROUND(SUM(Sales), 2) AS total_spent,
           COUNT(*) AS orders
       FROM orders
       GROUP BY `Customer Name`
       ORDER BY total_spent DESC
       LIMIT 5
   """)
   print("Top 5 clienti:")
   q2.show()
   ```

7. **Query 3 - Categoria più venduta per regione:**

   ```python
   q3 = spark.sql("""
       SELECT Region, Category,
              ROUND(SUM(Sales), 2) AS total,
              DENSE_RANK() OVER (
                  PARTITION BY Region ORDER BY SUM(Sales) DESC
              ) AS rnk
       FROM orders
       GROUP BY Region, Category
   """)
   q3.filter(col("rnk") == 1).show()
   ```

### Fase 4: Salva in MongoDB (5 minuti)

8. **Converti i risultati in dizionari e inserisci:**

   ```python
   from pymongo import MongoClient

   client = MongoClient("mongodb://localhost:27017/")
   db = client["lab22_pipeline"]

   # Salva vendite per regione
   col_region = db["sales_by_region"]
   col_region.delete_many({})
   rows = q1.collect()
   docs = [row.asDict() for row in rows]
   col_region.insert_many(docs)
   print(f"Inseriti in sales_by_region: {col_region.count_documents({})}")

   # Salva top clienti
   col_top = db["top_customers"]
   col_top.delete_many({})
   rows2 = q2.collect()
   docs2 = [row.asDict() for row in rows2]
   col_top.insert_many(docs2)
   print(f"Inseriti in top_customers: {col_top.count_documents({})}")
   ```

### Fase 5: Verifica in MongoDB (5 minuti)

9. **In `mongosh`, verifica i dati:**

   ```javascript
   use lab22_pipeline

   // Conta documenti
   db.sales_by_region.countDocuments()
   db.top_customers.countDocuments()

   // Vediamo un esempio
   db.sales_by_region.find({ Region: "East" }).sort({ order_year: 1 })

   // Top customer
   db.top_customers.find().sort({ total_spent: -1 }).limit(1)
   ```

10. **Crea indice e query:**

    ```javascript
    db.sales_by_region.createIndex({ Region: 1, order_year: 1 });
    db.sales_by_region.find({ Region: "West", order_year: 2017 });
    ```

## Output atteso

- ~9.800 righe caricate, pulite e arricchite con data/anno
- 3 query Spark SQL con risultati
- Documenti salvati in 2 collezioni MongoDB
- Indice creato e query verificata

## Checkpoint

- [ ] CSV caricato (~9.800 righe)
- [ ] Duplicati e null rimossi
- [ ] Date convertite e anno estratto
- [ ] 3 query Spark SQL eseguite
- [ ] Risultati salvati in MongoDB (2 collezioni)
- [ ] Indice creato e query funzionante

## Troubleshooting

| Problema                        | Soluzione                                             |
| ------------------------------- | ----------------------------------------------------- |
| "Column 'Order ID' not found"   | Usa backtick: `` `Order ID` ``                        |
| `insert_many` errore vuoto      | Controlla che `docs` non sia lista vuota              |
| Numeri come stringhe in MongoDB | In PySpark usa `inferSchema=True` per ottenere numeri |

## Cleanup

```javascript
use lab22_pipeline
db.dropDatabase()
```

```python
spark.stop()
client.close()
```
