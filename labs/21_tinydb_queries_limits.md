# Lab 21 - TinyDB: query utili, tabelle di riepilogo, e limiti pratici

## Obiettivo

Usare TinyDB in modo più intelligente: cercare con condizioni combinate, aggiornare etichette, e creare una piccola tabella di riepilogo invece di rileggere sempre i dati grezzi.

## Durata

30 minuti

## Prerequisiti

- Lab 17 completato
- Lab 20 completato

## Step

### Fase 1: Apri il database Superstore (5 minuti)

```python
from tinydb import TinyDB, Query

db = TinyDB("superstore_tinydb.json")
orders = db.table("orders")
Order = Query()

print(f"Documenti disponibili: {len(orders)}")
```

### Fase 2: Query combinate (10 minuti)

Scrivi query con condizioni multiple usando `&` (AND):

```python
# 1. Cerca ordini nella regione "West" con categoria "Technology"
# 2. Cerca ordini con sales > 500
# 3. Cerca ordini nella regione "West" con sales > 500

# Scrivi qui le tue 3 query
# Suggerimento: usa (Order.campo == "valore") & (Order.campo > numero)

# Stampa i conteggi e i primi 3 risultati della terza query
```

**Suggerimento**: usa `orders.search((Order.region == "West") & (Order.category == "Technology"))`.

### Fase 3: Aggiungi una label (5 minuti)

Aggiungi il campo `"high_value": True` a tutti i documenti con `sales > 500`, poi verifica cercando i documenti con `high_value == True`.

```python
# 1. Aggiorna i documenti con sales > 500, aggiungendo high_value: True
# 2. Cerca e stampa i primi 3 documenti con high_value == True

# Scrivi qui il tuo codice
```

**Suggerimento**: usa `orders.update({"high_value": True}, Order.sales > 500)`.

### Fase 4: Crea una tabella summary (10 minuti)

Crea una tabella `sales_by_region` con un documento per ogni regione contenente: `region`, `num_orders`, `total_sales`.

```python
summary = db.table("sales_by_region")
summary.truncate()

all_orders = orders.all()

# 1. Trova tutte le regioni uniche (usa un set)
# 2. Per ogni regione, filtra gli ordini e calcola:
#    - num_orders: numero di ordini
#    - total_sales: somma delle vendite (arrotondato a 2 decimali)
# 3. Inserisci i risultati nella tabella summary

# Scrivi qui il tuo codice

print(summary.all())
```

**Suggerimento**: crea una lista di dizionari e usa `summary.insert_multiple([...])`.

Poi scrivi 3 righe:

- perché una tabella `sales_by_region` è più comoda di rileggere tutti gli ordini ogni volta?
- perché TinyDB va bene per questo riepilogo ma non per dataset enormi?

## Output atteso

- Query combinate funzionanti
- Campo `high_value` aggiunto
- Tabella `sales_by_region` creata
- Riflessione scritta sui limiti di TinyDB

## Checkpoint

- [ ] Query combinate eseguite
- [ ] Label aggiunta con update
- [ ] Summary table creata
- [ ] Limiti di TinyDB spiegati
