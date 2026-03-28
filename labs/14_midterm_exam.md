# Lab 14 - Verifica intermedia (Midterm)

## Obiettivo

Sostenere la verifica intermedia che copre le lezioni 01-13: Introduzione ai Big Data, Architetture Distribuite, Programmazione e Architettura (PySpark, Spark SQL, formati file, streaming, pipeline batch).

**Nessuna domanda su TinyDB o NoSQL** - quei temi saranno nell'esame finale.

## Durata

Intera lezione (2 ore)

## Prerequisiti

- Ambiente PySpark funzionante
- Il tuo dataset: `serie_a_coppa_italia_2015_2023.csv` **oppure** `superstore_sales.csv`

---

## Struttura della prova

### Parte A - Teoria (30 minuti)

- Risposte brevi in celle Markdown del notebook
- 5 domande a risposta aperta
- 2-3 frasi per domanda
- Copertura: 5V, architetture distribuite, lazy evaluation, formati file, streaming

### Parte B - PySpark pratico (1 ora 20 minuti)

- Uso del dataset scelto nel corso
- Attività pratiche in PySpark e Spark SQL
- Possibili aree: ingestione, pulizia, trasformazione, query SQL, salvataggio in Parquet
- L'esatto testo della prova viene fornito dal docente durante l'esame

## Consegna

- Salva il notebook come: `midterm_[cognome].ipynb`
- Consegna il file al docente prima della fine della lezione

## Valutazione

| Parte               | Peso |
| ------------------- | ---- |
| A - Teoria          | 30%  |
| B - PySpark Pratico | 70%  |

**Soglia sufficienza**: 60/100.
**Peso sulla valutazione finale del corso**: 50%.

## Regole

- Lavoro individuale
- Puoi consultare i tuoi appunti e la documentazione PySpark
- Non puoi usare assistenti AI o soluzioni già pronte
- Il docente fornisce il testo completo della prova all'inizio della lezione

## Troubleshooting

| Problema                 | Soluzione                             |
| ------------------------ | ------------------------------------- |
| Spark non parte          | Riavvia il kernel Jupyter             |
| CSV non si carica        | Verifica il percorso del file         |
| Errore colonne con spazi | Usa backtick: ``col("`Order Date`")`` |

## Output atteso

- Notebook consegnato entro la fine della lezione
- Risposte teoriche complete
- Parte pratica eseguibile
- File salvato con il nome corretto

## Cleanup

```python
spark.stop()
```
