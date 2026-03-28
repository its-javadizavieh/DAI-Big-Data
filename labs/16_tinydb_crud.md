# Lab 16 - TinyDB: setup e operazioni CRUD

## Obiettivo

Installare TinyDB e praticare le operazioni CRUD (Create, Read, Update, Delete) usando semplici dati di partite di Serie A.

## Durata

30 minuti

## Prerequisiti

- Python attivo nel virtual environment
- Lezione 16 letta

## Step

### Fase 1: Installazione (5 minuti)

1. Installa TinyDB:

```bash
pip install tinydb
```

2. Crea un nuovo file Python o notebook.

### Fase 2: Crea il database e inserisci dati (10 minuti)

```python
from tinydb import TinyDB, Query

db = TinyDB("serie_a_db.json")
partite = db.table("partite")
partite.truncate()

partite.insert_multiple([
    {"home": "Juventus", "away": "Roma", "homeGoals": 3, "awayGoals": 1, "city": "Torino"},
    {"home": "Milan", "away": "Inter", "homeGoals": 1, "awayGoals": 2, "city": "Milano"},
    {"home": "Napoli", "away": "Lazio", "homeGoals": 2, "awayGoals": 0, "city": "Napoli"},
    {"home": "Fiorentina", "away": "Atalanta", "homeGoals": 0, "awayGoals": 0, "city": "Firenze"},
    {"home": "Torino", "away": "Bologna", "homeGoals": 4, "awayGoals": 2, "city": "Torino"}
])
```

### Fase 3: Read (7 minuti)

```python
Match = Query()

print(partite.all())
print(partite.search(Match.homeGoals > 2))
print(partite.search(Match.city == "Torino"))
print(partite.get(Match.home == "Napoli"))
```

### Fase 4: Update e Delete (8 minuti)

```python
partite.update({"competition": "Serie A"}, Match.home == "Juventus")
partite.update({"draw": True}, (Match.homeGoals == Match.awayGoals))
partite.remove(Match.away == "Atalanta")

print(f"Documenti finali: {len(partite)}")
```

## Output atteso

- File `serie_a_db.json` creato
- 5 documenti inseriti
- Query funzionanti
- 1 documento rimosso
- Conteggio finale: 4 documenti

## Checkpoint

- [ ] TinyDB installato
- [ ] File JSON creato
- [ ] Query con `Query()` eseguite
- [ ] Update eseguito
- [ ] Delete eseguito
