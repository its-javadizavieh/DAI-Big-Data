# Lab 19 - Aggregazioni avanzate e Spark SQL

## Obiettivo

Usare Spark SQL e window function per analisi avanzate sul dataset Superstore: ranking, running total e subtotali per gruppo.

## Durata

30 minuti

## Prerequisiti

- Lab 09 (Spark SQL) completato
- Dataset `superstore_sales.csv` nella cartella di lavoro

## Dataset

**superstore_sales.csv** - 9.800 ordini. Useremo: `Order Date`, `Region`, `State`, `City`, `Category`, `Sub-Category`, `Sales`.

---

## Step

### Fase 1: Carica e prepara (5 minuti)

1. **Carica il dataset:**

   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import col, to_date, year, month, sum, avg, count
   from pyspark.sql.window import Window
   from pyspark.sql.functions import dense_rank, lag, round as spark_round

   spark = SparkSession.builder.appName("Lab17_Advanced").getOrCreate()

   df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
   ```

2. **Aggiungi colonne anno e mese:**

   ```python
   df = (df
       .withColumn("order_date", to_date(col("`Order Date`"), "dd/MM/yyyy"))
       .withColumn("order_year", year(col("order_date")))
       .withColumn("order_month", month(col("order_date")))
   )

   df.createOrReplaceTempView("sales")
   df.show(5)
   ```

### Fase 2: Top-N per gruppo con DENSE_RANK (8 minuti)

3. **Trova le top 3 sotto-categorie per vendite in ogni categoria:**

   Usa una subquery con `DENSE_RANK() OVER (PARTITION BY ... ORDER BY ...)` per rankare le sotto-categorie per vendite totali all'interno di ogni categoria, poi filtra solo rank <= 3.

   ```python
   top_n = spark.sql("""
       SELECT category, sub_category, total_sales, rank_pos
       FROM (
           SELECT
               Category AS category,
               `Sub-Category` AS sub_category,
               ROUND(SUM(Sales), 2) AS total_sales,
               DENSE_RANK() OVER (
                   PARTITION BY ___
                   ORDER BY ___
               ) AS rank_pos
           FROM sales
           GROUP BY ___
       )
       WHERE ___
       ORDER BY category, rank_pos
   """)

   print("Top 3 sotto-categorie per categoria:")
   top_n.show(12)
   ```

   **Suggerimento**: `PARTITION BY Category` e `ORDER BY SUM(Sales) DESC`.

### Fase 3: Andamento mese su mese con LAG (8 minuti)

4. **Calcola vendite mensili per regione:**

   ```python
   monthly = spark.sql("""
       SELECT
           Region,
           order_year,
           order_month,
           ROUND(SUM(Sales), 2) AS monthly_sales
       FROM sales
       GROUP BY Region, order_year, order_month
       ORDER BY Region, order_year, order_month
   """)
   monthly.createOrReplaceTempView("monthly_sales")
   ```

5. **Aggiungi il mese precedente con LAG:**

   Usa `Window.partitionBy("Region").orderBy("order_year", "order_month")` e la funzione `lag` per calcolare la differenza rispetto al mese precedente.

   ```python
   w = Window.partitionBy(___).orderBy(___)

   mom = (
       monthly
       .withColumn("prev_month_sales", lag(___, ___).over(w))
       .withColumn("change", spark_round(
           ___, 2
       ))
   )

   print("Andamento mese su mese per regione:")
   mom.filter(col("Region") == "South").show(10)
   ```

   **Suggerimento**: `lag("monthly_sales", 1)` prende il valore precedente. La differenza e' `monthly_sales - prev_month_sales`.

### Fase 4: Running total (5 minuti)

6. **Calcola il totale cumulativo delle vendite per regione:**

   Crea una window con `partitionBy("Region")`, `orderBy("order_year", "order_month")` e `rowsBetween(Window.unboundedPreceding, Window.currentRow)` per calcolare la somma cumulativa.

   ```python
   w_cum = Window.partitionBy(___) \
       .orderBy(___) \
       .rowsBetween(___, ___)

   running = monthly.withColumn(
       "cumulative_sales",
       # Scrivi qui il tuo codice: sum cumulativa di monthly_sales
   )

   print("Running total vendite per regione:")
   running.filter(col("Region") == "East").show(10)
   ```

   **Suggerimento**: `spark_round(sum("monthly_sales").over(w_cum), 2)`.

### Fase 5: ROLLUP - Subtotali (4 minuti)

7. **Crea subtotali per regione e stato:**

   Usa `GROUP BY ROLLUP(Region, State)` per generare subtotali automatici. Le righe con null nel risultato rappresentano i subtotali.

   ```python
   rollup_result = spark.sql("""
       SELECT
           Region,
           State,
           ROUND(SUM(Sales), 2) AS total_sales,
           COUNT(*) AS num_orders
       FROM sales
       GROUP BY ___
       ORDER BY Region, State
   """)

   print("ROLLUP (nota le righe con null = subtotale):")
   rollup_result.show(15)
   print(f"Righe ROLLUP: {rollup_result.count()}")
   ```

   **Suggerimento**: sostituisci `___` con `ROLLUP(Region, State)`.

## Output atteso

- Top 3 sotto-categorie per ogni categoria (con rank)
- Andamento mese-su-mese con variazione
- Running total cumulativo
- Subtotali ROLLUP con null per i totali di gruppo

## Checkpoint

- [ ] `DENSE_RANK` mostra rank 1, 2, 3 per categoria
- [ ] `LAG` mostra null per il primo mese (corretto)
- [ ] Running total è sempre crescente
- [ ] ROLLUP mostra righe con null (= subtotali)

## Troubleshooting

| Problema               | Soluzione                                            |
| ---------------------- | ---------------------------------------------------- |
| "Column not found"     | Colonne con spazi: usa backtick `` `Sub-Category` `` |
| Window function errore | Serve sia `partitionBy` che `orderBy`                |
| LAG tutto null         | Controlla l'ordinamento nella window                 |

## Cleanup

```python
spark.stop()
```
