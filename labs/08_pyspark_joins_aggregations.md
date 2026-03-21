# Lab 08 — PySpark: join e aggregazioni

## Obiettivo

Eseguire aggregazioni con groupBy/agg, creare pivot table, e fare join tra DataFrames su un dataset di vendite.

## Durata (timebox)

30 minuti

## Prerequisiti

- Lab 06-07 completati
- Dataset CSV con colonne numeriche e categoriche (es. "Global Superstore Sales" da Kaggle)
- Un secondo CSV o DataFrame da creare per il join (es. tabella paesi)

## Scenario

Il marketing ti chiede: "Qual è la media delle vendite per dipartimento? E per anno?" Devi anche incrociare i dati vendite con un'anagrafica clienti.

## Step

1. **Carica il dataset** principale.
2. **groupBy + count**: conta le righe per ogni categoria.
   ```python
   df.groupBy("category").count().show()
   ```
3. **groupBy + agg multipla**: calcola sum, avg, max di una colonna numerica per categoria.
4. **Crea una pivot table**: vendite per anno e trimestre.
   ```python
   df.groupBy("year").pivot("quarter").sum("sales").show()
   ```
5. **Crea un secondo DataFrame**: una tabella di riferimento con 5-10 righe (es. categorie con descrizione).
   ```python
   lookup = spark.createDataFrame([("Tech", "Technology"), ("Furn", "Furniture")], ["code", "description"])
   ```
6. **Inner join**: unisci il dataset principale con la tabella di lookup.
7. **Left anti join**: trova le righe del dataset principale che NON hanno corrispondenza nella lookup.

## Output atteso

- Aggregazioni stampate per almeno 2 gruppi diversi
- Pivot table visibile con anni come righe e trimestri come colonne
- Join result con almeno 5 righe mostrate

## Checkpoint

- [ ] almeno 3 funzioni di aggregazione usate (sum, avg, count, etc.)
- [ ] Pivot table ha le colonne Q1, Q2, Q3, Q4
- [ ] Inner join riduce le righe; left_anti mostra le righe orfane

## Troubleshooting rapido

- "AnalysisException: Reference 'column' is ambiguous": usa `df1["col"]` invece di `col("col")` dopo un join
- "Pivot non mostra tutte le colonne": specifica i valori nella pivot: `.pivot("quarter", ["Q1", "Q2", "Q3", "Q4"])`

## Cleanup obbligatorio

```python
spark.stop()
```

## Parole chiave Google (screenshot/guide)

- "pyspark groupby agg multiple functions"
- "pyspark pivot table example"
- "pyspark join types explained"
