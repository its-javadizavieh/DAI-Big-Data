# Lab 07 - PySpark azioni e lazy evaluation

## Obiettivo

Comprendere la differenza tra trasformazioni e azioni, visualizzare il piano di esecuzione e sperimentare il caching.

## Durata (timebox)

30 minuti

## Prerequisiti

- Lab 06 completato (trasformazioni PySpark)
- Dataset CSV disponibile

## Scenario

Devi analizzare le performance di un dataset di vendite e capire se il caching migliora i tempi di esecuzione.

## Step

1. **Carica il dataset** e crea la SparkSession.
2. **Applica trasformazioni**: crea una catena di filter → select → withColumn (come nel Lab 06).
3. **Chiama diverse azioni**: prova `show(5)`, `count()`, `first()`, `describe().show()` sul risultato.
4. **Usa explain()**:
   ```python
   df_result = df.filter(...).select(...).withColumn(...)
   df_result.explain(True)
   ```
   Leggi il piano e identifica: quale filtro viene applicato? Quante colonne vengono lette?
5. **Sperimenta il caching**:

   ```python
   import time
   df_filtered = df.filter(col("Sales") > 100).cache()

   start = time.time()
   df_filtered.count()
   print(f"Prima esecuzione (cache fill): {time.time() - start:.2f}s")

   start = time.time()
   df_filtered.count()
   print(f"Seconda esecuzione (da cache): {time.time() - start:.2f}s")
   ```

6. **Rilascia la cache**: `df_filtered.unpersist()`

## Output atteso

- Output di explain() incollato nel notebook con annotazioni
- Tempi di esecuzione prima/dopo cache stampati

## Checkpoint

- [ ] explain() mostra il piano con almeno 2 step
- [ ] La seconda esecuzione con cache è più veloce della prima
- [ ] unpersist() eseguito senza errori

## Troubleshooting rapido

- "explain() non mostra nulla": assicurati di chiamare explain() su un DataFrame, non su un'azione
- "Il caching non migliora i tempi": su dataset piccoli la differenza può essere minima - è normale

## Cleanup obbligatorio

```python
df_filtered.unpersist()
spark.stop()
```

## Parole chiave Google (screenshot/guide)

- "pyspark explain execution plan"
- "pyspark cache persist example"
- "spark lazy evaluation tutorial"
