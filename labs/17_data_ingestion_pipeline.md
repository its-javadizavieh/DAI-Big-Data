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

Controlla quanti null ci sono per ogni colonna.

```python
from pyspark.sql.functions import col, count, when

# Scrivi il codice per contare i null per ogni colonna
# Suggerimento: usa count(when(col(c).isNull(), c)).alias(c) per ogni colonna
null_counts = df.select([
    # Scrivi qui il tuo codice
])
null_counts.show()
```

### Fase 3: Clean (8 minuti)

Pulisci il DataFrame applicando queste operazioni in sequenza:

1. Rimuovi i duplicati
2. Rimuovi le righe senza `Order ID` o `Sales`
3. Converti `Order Date` in tipo data (formato `dd/MM/yyyy`) e crea la colonna `order_date`
4. Estrai anno e mese in colonne separate (`order_year`, `order_month`)

```python
from pyspark.sql.functions import to_date, year, month

clean_df = (
    df
    # Scrivi qui il tuo codice
)
```

**Suggerimento**: usa `dropDuplicates()`, `dropna(subset=[...])`, `withColumn` + `to_date`, `year()`, `month()`.

### Fase 4: Load (8 minuti)

Seleziona le colonne da salvare in TinyDB e salva i documenti.

Colonne da selezionare (rinominandole):

- `Order ID` -> `order_id`
- `Customer Name` -> `customer_name`
- `Region` -> `region`
- `Category` -> `category`
- `Sub-Category` -> `sub_category`
- `Sales` -> `sales`
- `order_year`, `order_month`

```python
from tinydb import TinyDB

# 1. Seleziona e rinomina le colonne
to_store = clean_df.select(
    # Scrivi qui il tuo codice con col(...).alias(...)
)

# 2. Crea il database TinyDB e inserisci i documenti
# Suggerimento: usa [row.asDict() for row in to_store.collect()]

# Scrivi qui il tuo codice
```

### Fase 5: Verifica (4 minuti)

Verifica che il numero di righe Spark sia uguale al numero di documenti TinyDB.

```python
# Scrivi il codice per:
# 1. Stampare il conteggio righe di to_store (Spark)
# 2. Stampare il conteggio documenti nella tabella TinyDB
# 3. Stampare i primi 3 documenti

# Scrivi qui il tuo codice
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
