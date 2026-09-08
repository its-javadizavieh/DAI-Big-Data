# Soluzioni Lab 14 — Famiglie NoSQL, confronto con SQL, e TinyDB

## Fase 1: Classifica gli scenari

1. Sessioni utente di un sito web -> **Key-value**
2. Catalogo prodotti con attributi diversi -> **Document**
3. Log di sensori scritti in grandi volumi -> **Column-family**
4. Rete sociale con relazioni tra utenti -> **Graph**

## Fase 2: Confronto SQL vs documenti

- **SQL**: richiederebbe una o più tabelle con colonne fisse; per prodotti molto diversi servirebbero colonne vuote o più tabelle collegate.
- **Documenti**: ogni prodotto può avere solo i campi che gli servono, ad esempio RAM per laptop o taglia per vestiti.
- Per questo scenario il modello documentale è più semplice e più naturale.

## Fase 3: Perché TinyDB?

1. TinyDB è più semplice di MongoDB perché si installa con `pip install tinydb`, non richiede server, e si usa direttamente da Python.
2. Comando corretto:

```bash
pip install tinydb
```

3. TinyDB non è sufficiente quando:

- servono più utenti contemporanei
- serve un database server condiviso
- il progetto cresce molto