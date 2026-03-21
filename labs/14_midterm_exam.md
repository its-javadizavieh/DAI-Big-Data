# Lab 14 - Verifica intermedia (Midterm)

## Obiettivo

Completare la verifica intermedia che copre le lezioni 01-13: Introduzione ai Big Data, Architetture Distribuite, Programmazione e Architettura (PySpark, Spark SQL, formati file, streaming, pipeline batch).

**Nessuna domanda su MongoDB o NoSQL** - quei temi saranno nell'esame finale.

## Durata

Intera lezione (2 ore)

## Prerequisiti

- Ambiente PySpark funzionante
- Il tuo dataset: `serie_a_coppa_italia_2015_2023.csv` **oppure** `superstore_sales.csv`

---

## Parte A - Teoria (30 minuti)

Rispondi alle domande in celle Markdown nel notebook. Spiega con parole tue (2-3 frasi per domanda).

1. Definisci le **5V dei Big Data** e fai un esempio pratico per ciascuna.
2. Spiega la differenza tra **scalabilità verticale (scale-up) e orizzontale (scale-out)**. Perché per Big Data si preferisce scale-out?
3. Cos'è la **lazy evaluation** in Spark? Perché è utile rispetto all'esecuzione immediata?
4. Confronta **Parquet** e **CSV**: elenca 3 vantaggi specifici di Parquet per analytics.
5. Cos'è lo **Spark Streaming**? Spiega la differenza tra elaborazione batch e micro-batch.

## Parte B - PySpark Pratico (1 ora 20 minuti)

Usa il TUO dataset scelto all'inizio del corso.

1. **Ingestione** (5 pt): Carica il CSV con PySpark. Stampa righe, colonne e schema.
2. **Pulizia** (10 pt): Rimuovi null nelle colonne chiave e duplicati. Stampa quante righe sono state rimosse.
3. **Trasformazione** (10 pt): Crea almeno 2 colonne derivate (es. total_goals, result, year, month / sales_level, year). Mostra l'output.
4. **Spark SQL - Query 1** (15 pt): Registra una vista temporanea. Scrivi una query GROUP BY con almeno 2 funzioni aggregate (SUM, AVG, COUNT).
5. **Spark SQL - Query 2** (15 pt): Scrivi una query con HAVING oppure una subquery.
6. **Spark SQL - Query 3** (10 pt): Scrivi una query con window function (RANK, ROW_NUMBER o simili).
7. **Salvataggio** (5 pt): Salva il dataset pulito in Parquet partizionato per anno. Confronta la dimensione con il CSV originale.

## Consegna

- Salva il notebook come: `midterm_[cognome].ipynb`
- Consegna il file al docente prima della fine della lezione

## Valutazione

| Parte | Peso |
|-------|------|
| A - Teoria | 30% |
| B - PySpark Pratico | 70% |

**Soglia sufficienza**: 60/100.
**Peso sulla valutazione finale del corso**: 50%.

## Troubleshooting

| Problema | Soluzione |
|----------|-----------|
| Spark non parte | Riavvia il kernel Jupyter |
| CSV non si carica | Verifica il percorso del file |
| Errore colonne con spazi | Usa backtick: `` col("`Order Date`") `` |

## Cleanup

```python
spark.stop()
```
