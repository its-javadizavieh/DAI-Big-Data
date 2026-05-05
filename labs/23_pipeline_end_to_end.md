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

Pulisci il DataFrame:

1. Rimuovi duplicati
2. Rimuovi righe senza `Order ID` o `Sales`
3. Converti `Order Date` in tipo data (formato `dd/MM/yyyy`) e crea la colonna `order_date`
4. Estrai l'anno in una colonna `order_year`

```python
clean = (
    raw
    # Scrivi qui il tuo codice di pulizia
)
```

**Suggerimento**: usa `dropDuplicates()`, `dropna(subset=[...])`, `withColumn` + `to_date`, `year()`.

### Fase 3: Analisi con Spark SQL (10 minuti)

```python
clean.createOrReplaceTempView("orders")
```

**Query 1**: Per ogni regione e anno, calcola le vendite totali e il numero di ordini. Ordina per regione e anno.

```python
q1 = spark.sql("""
    -- Scrivi la query: vendite per regione e anno
    -- Colonne: Region, order_year, total_sales, num_orders
    SELECT
        ___
    FROM orders
    GROUP BY ___
    ORDER BY ___
""")
q1.show()
```

**Query 2**: Trova i top 5 clienti per spesa totale.

```python
q2 = spark.sql("""
    -- Scrivi la query: top 5 clienti per spesa
    -- Colonne: customer, total_spent
    SELECT
        ___
    FROM orders
    GROUP BY ___
    ORDER BY ___
    LIMIT 5
""")
q2.show()
```

### Fase 4: Salva i risultati (5 minuti)

Salva i risultati delle due query in TinyDB, in due tabelle separate: `sales_by_region` e `top_customers`.

```python
from tinydb import TinyDB

db = TinyDB("pipeline_results.json")

# 1. Crea le tabelle "sales_by_region" e "top_customers"
# 2. Svuota le tabelle (truncate)
# 3. Inserisci i risultati di q1 e q2
# Suggerimento: usa [row.asDict() for row in q1.collect()]

# Scrivi qui il tuo codice
```

### Fase 5: Verifica (5 minuti)

Verifica che i conteggi Spark e TinyDB corrispondano.

```python
# Stampa:
# 1. Numero di righe q1 (Spark) vs documenti nella tabella region
# 2. Numero di righe q2 (Spark) vs documenti nella tabella customers
# 3. I primi 3 documenti di ogni tabella

# Scrivi qui il tuo codice
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
