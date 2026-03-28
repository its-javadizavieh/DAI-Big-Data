# Lab Datasets - Guida per gli Studenti

In questo corso utilizziamo **2 dataset reali** che troverai nella cartella `labs/`. Questi file CSV saranno usati in quasi tutti i lab del corso. **Non modificarli** e **non cancellarli**.

---

## 1. Serie A & Coppa Italia (serie_a_coppa_italia_2015_2023.csv)

**Tema:** Calcio italiano - partite di Serie A e Coppa Italia

| Info         | Dettaglio                                            |
| ------------ | ---------------------------------------------------- |
| Righe        | 3.788 partite                                        |
| Colonne      | ~230 (molte sono dati dei giocatori)                 |
| Periodo      | Stagioni 2014/15 – 2022/23                           |
| Competizioni | Serie A (3.420 partite) + Coppa Italia (368 partite) |

**Colonne principali** (quelle che useremo di più):

| Colonna               | Tipo   | Esempio           | Descrizione                           |
| --------------------- | ------ | ----------------- | ------------------------------------- |
| `ID`                  | int    | 8529              | Identificativo unico della partita    |
| `Competition_Name`    | string | "Serie A"         | Nome competizione                     |
| `Season_End_Year`     | int    | 2015              | Anno di fine stagione                 |
| `Date`                | string | "8/30/2014"       | Data della partita (formato M/D/YYYY) |
| `Home`                | string | "Juventus"        | Squadra di casa                       |
| `Away`                | string | "Roma"            | Squadra in trasferta                  |
| `HomeGoals`           | int    | 3                 | Gol squadra di casa                   |
| `AwayGoals`           | int    | 1                 | Gol squadra in trasferta              |
| `Venue`               | string | "Allianz Stadium" | Nome dello stadio                     |
| `venue_city`          | string | "Torino"          | Città dello stadio                    |
| `Referee`             | string | "Daniele Orsato"  | Arbitro                               |
| `formation_home`      | string | "4-3-3"           | Formazione squadra di casa            |
| `formation_away`      | string | "3-5-2"           | Formazione squadra in trasferta       |
| `possessiontime_home` | float  | 0.58              | Possesso palla casa (0-1)             |
| `possessiontime_away` | float  | 0.42              | Possesso palla trasferta (0-1)        |
| `shots_total_home`    | int    | 15                | Tiri totali casa                      |
| `shots_ongoal_home`   | int    | 6                 | Tiri in porta casa                    |
| `yellow_cards_home`   | int    | 2                 | Cartellini gialli casa                |
| `red_cards_home`      | int    | 0                 | Cartellini rossi casa                 |
| `fouls_home`          | int    | 12                | Falli commessi casa                   |

> **Nota:** Il dataset ha anche dati dettagliati sui giocatori (formazione titolare e panchina), ma per la maggior parte dei lab useremo solo le colonne principali elencate sopra.

---

## 2. Superstore Sales (superstore_sales.csv)

**Tema:** Vendite di un negozio retail negli USA

| Info    | Dettaglio              |
| ------- | ---------------------- |
| Righe   | 9.800 ordini           |
| Colonne | 18                     |
| Periodo | 2017–2020              |
| Ambito  | Stati Uniti, 4 regioni |

**Tutte le colonne:**

| Colonna         | Tipo   | Esempio            | Descrizione                                      |
| --------------- | ------ | ------------------ | ------------------------------------------------ |
| `Row ID`        | int    | 1                  | Riga sequenziale                                 |
| `Order ID`      | string | "CA-2017-152156"   | ID dell'ordine                                   |
| `Order Date`    | string | "08/11/2017"       | Data ordine (DD/MM/YYYY)                         |
| `Ship Date`     | string | "11/11/2017"       | Data spedizione                                  |
| `Ship Mode`     | string | "Second Class"     | Modalità spedizione                              |
| `Customer ID`   | string | "CG-12520"         | ID cliente                                       |
| `Customer Name` | string | "Claire Gute"      | Nome cliente                                     |
| `Segment`       | string | "Consumer"         | Segmento (Consumer/Corporate/Home Office)        |
| `Country`       | string | "United States"    | Paese                                            |
| `City`          | string | "Henderson"        | Città                                            |
| `State`         | string | "Kentucky"         | Stato USA                                        |
| `Postal Code`   | int    | 42420              | CAP                                              |
| `Region`        | string | "South"            | Regione (East/West/Central/South)                |
| `Product ID`    | string | "FUR-BO-10001798"  | ID prodotto                                      |
| `Category`      | string | "Furniture"        | Categoria (Furniture/Office Supplies/Technology) |
| `Sub-Category`  | string | "Bookcases"        | Sotto-categoria                                  |
| `Product Name`  | string | "Bush Somerset..." | Nome prodotto                                    |
| `Sales`         | float  | 261.96             | Importo vendita in USD                           |

---

## Come caricare i dataset in PySpark

```python
# Serie A
calcio_df = spark.read.csv("serie_a_coppa_italia_2015_2023.csv", header=True, inferSchema=True)

# Superstore Sales
vendite_df = spark.read.csv("superstore_sales.csv", header=True, inferSchema=True)
```

## Come salvare i dataset in TinyDB (con Python)

```python
import csv
from tinydb import TinyDB

db = TinyDB("bigdata_course.json")
table = db.table("superstore")
table.truncate()

# Carica un CSV in TinyDB
with open("superstore_sales.csv", "r") as f:
    reader = csv.DictReader(f)
    docs = list(reader)
    table.insert_multiple(docs)
```
