# Lab 17 - Pipeline di ingestion: CSV → PySpark → MongoDB

## Obiettivo

Costruire una pipeline ETL semplice: caricare il CSV del Superstore con PySpark, pulire i dati, e inserirli in MongoDB.

## Durata

30 minuti

## Prerequisiti

- Lab 10 (formati file) e Lab 16 (MongoDB CRUD) completati
- MongoDB in esecuzione
- `pip install pymongo`

## Dataset

**superstore_sales.csv** - 9.800 ordini di un negozio retail USA. Colonne principali: `Order ID`, `Order Date`, `Ship Mode`, `Customer Name`, `Segment`, `Region`, `Category`, `Sub-Category`, `Sales`.

---

## Step

### Fase 1: Extract - Carica il CSV (5 minuti)

1. **Avvia Spark e carica il CSV:**

   ```python
   from pyspark.sql import SparkSession

   spark = SparkSession.builder.appName("Lab13_Ingestion").getOrCreate()

   # Carica il dataset Superstore
   df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)

   print(f"Righe caricate: {df.count()}")
   print(f"Colonne: {len(df.columns)}")
   df.printSchema()
   df.show(5)
   ```

### Fase 2: Validate - Controlla i dati (5 minuti)

2. **Conta i null per colonna:**

   ```python
   from pyspark.sql.functions import col, count, when

   null_counts = df.select([
       count(when(col(c).isNull(), c)).alias(c)
       for c in df.columns
   ])
   print("Nulli per colonna:")
   null_counts.show()
   ```

3. **Controlla i duplicati:**

   ```python
   total = df.count()
   unique = df.dropDuplicates().count()
   print(f"Righe totali: {total}")
   print(f"Righe uniche: {unique}")
   print(f"Duplicati: {total - unique}")
   ```

### Fase 3: Clean - Pulisci i dati (8 minuti)

4. **Rimuovi duplicati e null critici:**

   ```python
   clean_df = (
       df
       .dropDuplicates()
       .dropna(subset=["Order ID", "Sales"])
   )
   print(f"Dopo pulizia: {clean_df.count()} righe")
   ```

5. **Converti la data e aggiungi colonne anno/mese:**

   ```python
   from pyspark.sql.functions import to_date, year, month

   clean_df = (
       clean_df
       .withColumn("order_date_parsed", to_date(col("`Order Date`"), "dd/MM/yyyy"))
       .withColumn("order_year", year(col("order_date_parsed")))
       .withColumn("order_month", month(col("order_date_parsed")))
   )

   clean_df.select("Order Date", "order_date_parsed", "order_year", "order_month").show(5)
   ```

6. **Arrotonda gli importi:**

   ```python
   from pyspark.sql.functions import round as spark_round

   clean_df = clean_df.withColumn("Sales", spark_round(col("Sales"), 2))
   ```

### Fase 4: Load - Inserisci in MongoDB (8 minuti)

7. **Seleziona le colonne che ti servono:**

   ```python
   to_load = clean_df.select(
       col("`Order ID`").alias("order_id"),
       col("`Customer Name`").alias("customer_name"),
       col("Segment").alias("segment"),
       col("Region").alias("region"),
       col("Category").alias("category"),
       col("`Sub-Category`").alias("sub_category"),
       col("Sales").alias("sales"),
       col("order_year"),
       col("order_month")
   )
   to_load.show(5)
   ```

8. **Carica in MongoDB:**

   ```python
   from pymongo import MongoClient

   client = MongoClient("mongodb://localhost:27017/")
   db = client["lab13_db"]
   collection = db["superstore"]

   # Pulisci collezione (per idempotenza se ri-esegui)
   collection.delete_many({})

   # Converti a lista di dict e inserisci
   rows = to_load.toPandas().to_dict(orient="records")
   collection.insert_many(rows)

   print(f"Documenti inseriti in MongoDB: {collection.count_documents({})}")
   ```

### Fase 5: Verifica (4 minuti)

9. **Controlla in MongoDB:**

   ```python
   # Mostra 3 documenti
   print("Primi 3 documenti:")
   for doc in collection.find().limit(3):
       print(f"  {doc['customer_name']} - {doc['category']} - ${doc['sales']}")

   # Confronta conteggi
   spark_count = to_load.count()
   mongo_count = collection.count_documents({})
   print(f"\nSpark: {spark_count} righe")
   print(f"MongoDB: {mongo_count} documenti")
   print(f"Match: {'SI' if spark_count == mongo_count else 'NO'}")
   ```

## Output atteso

- CSV caricato: ~9.800 righe
- Nulli contati per ogni colonna
- Dati puliti con colonne anno/mese aggiunte
- ~9.800 documenti in MongoDB
- Conteggi Spark == MongoDB

## Checkpoint

- [ ] CSV caricato con schema corretto
- [ ] Nulli verificati
- [ ] Duplicati contati
- [ ] Date convertite + anno/mese aggiunti
- [ ] Documenti visibili in MongoDB
- [ ] Conteggi corrispondono

## Troubleshooting

| Problema                     | Soluzione                                                  |
| ---------------------------- | ---------------------------------------------------------- |
| "Connection refused" MongoDB | Avvia: `mongod` o `docker start mongo`                     |
| `to_date` restituisce null   | Prova formato `"MM/dd/yyyy"` se i dati sono in formato USA |
| `toPandas()` lento           | Il dataset ha ~10K righe, dovrebbe andare bene             |
| Backtick nelle colonne       | Colonne con spazi richiedono `` `Order ID` `` (backtick)   |

## Cleanup

```python
client = MongoClient("mongodb://localhost:27017/")
client.drop_database("lab13_db")
client.close()
spark.stop()
```
