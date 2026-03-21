# Lab 26 — Verifica finale

## Obiettivo

Verifica finale del corso: copre le lezioni 15-25 — NoSQL Databases, Data Ingestion & Processing, Data Storage & Scalability.

**I temi già verificati nel midterm (L01-L13) NON sono in questo esame.**

## Durata

3 ore

## Regole

- Lavoro **individuale**
- Puoi consultare: documentazione PySpark, MongoDB, i tuoi appunti personali
- **NON** puoi consultare: assistenti AI, soluzioni pre-fatte, lavori dei compagni
- Consegna: notebook `.ipynb` + comandi MongoDB in file `.txt`

## Valutazione

| Sezione | Peso |
|---------|------|
| Parte A — Teoria | 30% |
| Parte B1 — MongoDB | 35% |
| Parte B2 — Pipeline E2E | 35% |

**Soglia sufficienza**: 60/100.
**Voto finale corso** = 50% Midterm + 50% Esame Finale.

---

## Parte A — Teoria (30 minuti)

Rispondi in una cella Markdown del notebook (2-3 frasi per domanda).

### Domanda 1 (6 punti)

Elenca le 4 famiglie NoSQL (document, key-value, column-family, graph). Per ognuna, scrivi un caso d'uso reale in cui è la scelta migliore.

### Domanda 2 (6 punti)

Spiega la differenza tra **embedding** e **referencing** nella modellazione documenti MongoDB. Quando sceglieresti uno rispetto all'altro?

### Domanda 3 (6 punti)

Cos'è lo **sharding**? Spiega la differenza tra range sharding e hash sharding. In quale scenario è preferibile l'hash?

### Domanda 4 (6 punti)

Spiega il **CAP theorem**. Se un sistema deve essere altamente disponibile e tollerante alle partizioni, che compromesso deve accettare?

### Domanda 5 (6 punti)

Descrivi una **pipeline di data ingestion** completa (da CSV a MongoDB). Quali sono le fasi principali e quali strumenti/comandi useresti?

---

## Parte B1 — MongoDB Pratico (1 ora)

Lavora in `mongosh`.

### Esercizio B1.1 — CRUD (5 punti)

1. Crea il database `final_exam` e la collezione `products`
2. Inserisci almeno 8 documenti con campi: name, price, category, stock, tags (array)

### Esercizio B1.2 — Query (10 punti)

3. Trova tutti i prodotti in "Electronics" con price > 200
4. Trova i prodotti con stock < 10 (low stock alert)
5. Trova i prodotti che hanno "sale" nel loro array tags

### Esercizio B1.3 — Update (5 punti)

6. Aumenta il prezzo di tutti i prodotti "Electronics" del 10% usando `$mul`

### Esercizio B1.4 — Aggregation Pipeline (10 punti)

7. Scrivi un aggregation pipeline che raggruppa per category e calcola: totale stock e media prezzo per categoria

### Esercizio B1.5 — Index (5 punti)

8. Crea un indice su `{category: 1, price: -1}`
9. Usa `explain()` per dimostrare che l'indice viene usato

---

## Parte B2 — Pipeline E2E (1 ora)

Lavora in VS Code (estensione Jupyter) con PySpark + pymongo.

### Esercizio B2.1 — Caricamento (5 punti)

1. Carica il tuo dataset CSV con PySpark. Stampa righe e schema.

### Esercizio B2.2 — Pulizia e trasformazione (10 punti)

2. Rimuovi null e duplicati
3. Crea almeno 2 colonne derivate

### Esercizio B2.3 — Analisi Spark SQL (10 punti)

4. Registra una vista temporanea
5. Scrivi 2 query significative con GROUP BY e aggregazioni

### Esercizio B2.4 — Salvataggio in MongoDB (5 punti)

6. Inserisci i risultati delle query in MongoDB usando pymongo
7. Verifica con `count_documents()`

### Esercizio B2.5 — Verifica (5 punti)

8. In mongosh, interroga i risultati salvati e mostra che corrispondono all'output di Spark SQL

---

## Consegna

- Notebook `.ipynb` con Parte A (Markdown) e Parte B2 (codice PySpark + pymongo)
- File `.txt` con comandi MongoDB (Parte B1)
- Nome file: `final_[cognome].ipynb` e `final_mongo_[cognome].txt`

## Cleanup (dopo la consegna)

```javascript
use final_exam
db.dropDatabase()
```

```python
spark.stop()
```
