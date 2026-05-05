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

# Inserisci queste 5 partite usando insert_multiple:
data = [
    {"home": "Juventus", "away": "Roma", "homeGoals": 3, "awayGoals": 1, "city": "Torino"},
    {"home": "Milan", "away": "Inter", "homeGoals": 1, "awayGoals": 2, "city": "Milano"},
    {"home": "Napoli", "away": "Lazio", "homeGoals": 2, "awayGoals": 0, "city": "Napoli"},
    {"home": "Fiorentina", "away": "Atalanta", "homeGoals": 0, "awayGoals": 0, "city": "Firenze"},
    {"home": "Torino", "away": "Bologna", "homeGoals": 4, "awayGoals": 2, "city": "Torino"}
]

# Scrivi qui il tuo codice per inserire i dati
```

### Fase 3: Read (7 minuti)

```python
Match = Query()

# Scrivi le query per:
# 1. Stampare tutti i documenti nella tabella
# 2. Cercare le partite con homeGoals > 2
# 3. Cercare le partite giocate a "Torino"
# 4. Ottenere la partita del Napoli in casa (usa .get())

# Scrivi qui il tuo codice
```

**Suggerimento**: usa `partite.all()`, `partite.search(Match.campo == valore)`, `partite.get()`.

### Fase 4: Update e Delete (8 minuti)

```python
# Scrivi il codice per:
# 1. Aggiungere il campo "competition": "Serie A" alla partita della Juventus
# 2. Aggiungere il campo "draw": True alle partite in pareggio (homeGoals == awayGoals)
# 3. Rimuovere la partita con away == "Atalanta"
# 4. Stampare il conteggio finale dei documenti

# Scrivi qui il tuo codice
```

**Suggerimento**: usa `partite.update({campo: valore}, condizione)` e `partite.remove(condizione)`.

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
