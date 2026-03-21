# Lab 02 — Sistemi distribuiti: cluster, nodi, parallelismo

## Obiettivo

Capire come funziona un cluster disegnando un diagramma boss-helper e simulando la distribuzione dei dati.

## Durata (timebox)

30 minuti

## Prerequisiti

- Lab 01 completato (conoscenza delle 5V)
- Editor di testo oppure carta e penna per i diagrammi

## Scenario

La tua scuola vuole analizzare i risultati di TUTTI gli studenti italiani alle prove INVALSI: 2.5 milioni di studenti, con voti in matematica, italiano e inglese. I dati sono in un unico file CSV da 15 GB. Un singolo computer con 8 GB di RAM non può caricare il file intero in memoria.

Hai a disposizione un cluster di 5 computer:

- 1 computer **boss** (coordinatore)
- 4 computer **helper** (esecutori), ciascuno con 8 GB di RAM

## Step

1. **Disegna il cluster**: rappresenta graficamente (anche in modo semplice) il boss e i 4 helper.

   Esempio di diagramma semplice:

   ```
              +--------+
              |  BOSS  |
              +--------+
             /  |    |  \
            /   |    |   \
   +------+ +------+ +------+ +------+
   |Help 1| |Help 2| |Help 3| |Help 4|
   | 8 GB | | 8 GB | | 8 GB | | 8 GB |
   +------+ +------+ +------+ +------+
   ```

2. **Dividi il dataset**: il file è 15 GB e hai 4 helper. Rispondi:
   - In quante parti (partizioni) divideresti il file?
   - Quanto è grande ogni parte?
   - A quale helper assegni ogni parte?

   **Suggerimento**: 15 GB ÷ 4 helper ≈ 3.75 GB per helper (sta nella RAM da 8 GB!)

3. **Simula un compito**: il boss chiede "conta quanti studenti hanno preso più di 8 in matematica".
   - Descrivi cosa fa il boss
   - Descrivi cosa fa ogni helper
   - Come vengono combinati i risultati?

   Esempio:

   ```
   Boss: "Contate gli studenti con voto_mate > 8 nella vostra partizione"
   Helper 1: "Ho trovato 45.230 studenti"
   Helper 2: "Ho trovato 51.102 studenti"
   Helper 3: "Ho trovato 48.750 studenti"
   Helper 4: "Ho trovato 47.918 studenti"
   Boss: "Totale: 45.230 + 51.102 + 48.750 + 47.918 = 193.000 studenti"
   ```

4. **Simula un guasto**: l'Helper 3 si spegne a metà del lavoro. Descrivi cosa succede:
   - Come fa il boss a scoprire che l'Helper 3 è giù?
   - Cosa fa il boss per recuperare?
   - Il lavoro deve ricominciare da zero?

5. **Scale-up vs Scale-out**: compila questa tabella.

   |                         | Scale-up (computer più potente) | Scale-out (più computer) |
   | ----------------------- | ------------------------------- | ------------------------ |
   | Costo                   | ?                               | ?                        |
   | Limite massimo          | ?                               | ?                        |
   | Se un computer si rompe | ?                               | ?                        |
   | Per il nostro scenario  | ?                               | ?                        |

## Output atteso

- Diagramma del cluster con partizioni assegnate
- Spiegazione di come i 4 helper processano il compito
- Descrizione del recovery dopo il guasto dell'Helper 3
- Tabella scale-up vs scale-out compilata

## Checkpoint

- [ ] Il diagramma mostra chiaramente boss e 4 helper
- [ ] La divisione dei 15 GB è corretta (ogni helper riceve ~3.75 GB)
- [ ] La simulazione del compito mostra: boss assegna → helper conta → boss somma
- [ ] Il recovery è descritto correttamente (boss ri-assegna il lavoro dell'Helper 3)

## Troubleshooting rapido

| Problema                      | Soluzione                                                                             |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| Non so disegnare il diagramma | Usa rettangoli e frecce, come nell'esempio sopra                                      |
| Non capisco la divisione      | Pensa a dividere una pizza: 15 fette tra 4 persone                                    |
| Non so descrivere il recovery | Il boss controlla i "battiti" (heartbeat) — se non ne riceve, sa che l'helper è morto |

## Cleanup obbligatorio

- Nessun cleanup richiesto (lab teorico/cartaceo)

## Parole chiave Google (screenshot/guide)

- "cluster computing explained for beginners"
- "master worker architecture diagram"
- "horizontal vs vertical scaling simple explanation"
