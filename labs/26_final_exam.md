# Lab 26 - Verifica finale

## Obiettivo

Sostenere la verifica finale del corso sulle lezioni 15-25: famiglie NoSQL, TinyDB, pipeline PySpark, Spark SQL, Parquet, e confronto base TinyDB vs MongoDB.

## Durata

3 ore

## Regole

- Lavoro individuale
- Puoi consultare: documentazione PySpark, TinyDB, i tuoi appunti
- NON puoi consultare: assistenti AI, soluzioni pre-fatte, lavori dei compagni
- Consegna: un notebook `.ipynb`
- Il testo completo della prova viene fornito dal docente all'inizio dell'esame

## Valutazione

| Sezione                   | Peso |
| ------------------------- | ---- |
| Parte A - Teoria          | 30%  |
| Parte B1 - TinyDB pratico | 35%  |
| Parte B2 - Pipeline E2E   | 35%  |

## Struttura della prova

### Parte A - Teoria (30 minuti)

- 5 risposte brevi
- 2-3 frasi per domanda
- Aree: famiglie NoSQL, TinyDB, confronto TinyDB vs MongoDB, CAP theorem, pipeline dati

### Parte B1 - TinyDB pratico (1 ora)

- Attività in Python
- Possibili aree: creazione database locale, inserimento documenti, query, update, delete, summary table
- Focus su chiarezza del codice e correttezza dei risultati

### Parte B2 - Pipeline E2E (1 ora)

- Attività in PySpark e Spark SQL
- Possibili aree: caricamento CSV, pulizia, colonne derivate, query SQL, salvataggio in Parquet, salvataggio finale in TinyDB, verifica conteggi
- Il dettaglio operativo viene fornito dal docente durante l'esame

## Consegna

- Notebook `.ipynb` con teoria + pratica
- Nome file: `final_[cognome].ipynb`

## Output atteso

- Notebook completo e ordinato
- Risposte teoriche concise ma corrette
- Parte TinyDB funzionante
- Pipeline finale eseguibile
- File consegnato con il nome corretto

## Cleanup (dopo la consegna)

```python
spark.stop()
```
