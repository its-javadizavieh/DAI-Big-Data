# Lab 22 — Replicazione, consistenza e CAP theorem

## Obiettivo

Comprendere come funziona la replicazione in MongoDB, analizzare il CAP theorem e valutare configurazioni per scenari reali.

## Durata

30 minuti

## Prerequisiti

- Lab 20-21 completati
- Slide della lezione 22 lette

## Nota

Questo è un lab **teorico/progettuale** — non serve codice, lavorerai su carta o in un file Markdown.

---

## Step

### Fase 1: Traccia una write (10 minuti)

1. **Immagina un replica set con 3 server** (1 primary + 2 secondary).

   Un utente esegue questo comando:

   ```javascript
   db.partite.insertOne({ home: "Juventus", away: "Napoli", homeGoals: 2 });
   ```

   **Descrivi passo-passo cosa succede:**
   - Passo 1: Il client invia la write al \_\_\_\_
   - Passo 2: Il primary scrive i dati nel \_\_\_\_
   - Passo 3: L'operazione viene registrata nell'\_\_\_\_ (operation log)
   - Passo 4: I secondary leggono l'oplog e \_\_\_\_
   - Passo 5: Il primary invia l'\_\_\_\_ al client

2. **Failover:** Immagina che il primary si spenga.
   - Chi diventa il nuovo primary?
   - Quanto tempo ci vuole (circa)?
   - Le letture funzionano durante l'elezione?

### Fase 2: Analisi CAP (10 minuti)

3. **Il CAP theorem dice che in caso di partizione di rete, puoi avere SOLO uno tra:**
   - **C** (Consistency) — tutti leggono lo stesso dato
   - **A** (Availability) — il sistema risponde sempre

4. **Analizza 3 scenari. Per ognuno, scegli CP o AP:**

   | Scenario                           | CP o AP? | Write Concern | Read Preference | Perché? |
   | ---------------------------------- | -------- | ------------- | --------------- | ------- |
   | App di home banking                |          |               |                 |         |
   | Feed social media (like Instagram) |          |               |                 |         |
   | Prenotazione biglietti concerti    |          |               |                 |         |

   **Suggerimenti:**
   - Write concern: `w: 1` (veloce), `w: "majority"` (sicuro)
   - Read preference: `primary` (dati freschi), `secondary` (meno carico)

### Fase 3: Eventual consistency (10 minuti)

5. **Rispondi a queste domande:**

   a) Cos'è l'"eventual consistency"? Spiega con parole tue.

   b) **Scenario accettabile:** Descrivi un caso in cui l'eventual consistency va bene.
   - Esempio: "Il contatore dei like su un post social..."

   c) **Scenario NON accettabile:** Descrivi un caso in cui l'eventual consistency è pericolosa.
   - Esempio: "Il saldo del conto in banca..."

6. **Per il dataset di Serie A: quale configurazione useresti?**
   - I dati delle partite cambiano spesso?
   - Serve consistenza immediata o va bene eventual?
   - Quale write concern e read preference consiglieresti?

## Output atteso

- Descrizione step-by-step della write (5 passi)
- Descrizione del failover
- Tabella con 3 scenari analizzati (CP/AP)
- 2 esempi di eventual consistency (accettabile + non accettabile)
- Configurazione consigliata per il dataset Serie A

## Checkpoint

- [ ] Write descritta con oplog e replicazione
- [ ] Failover descritto con tempi (~10-12 secondi)
- [ ] 3 scenari CAP con CP/AP motivato
- [ ] Write concern e read preference specificati
- [ ] Eventual consistency: 1 esempio accettabile + 1 non accettabile

## Suggerimenti per le risposte

| Concetto                      | Definizione breve                                                      |
| ----------------------------- | ---------------------------------------------------------------------- |
| Oplog                         | Log delle operazioni sul primary, replicato ai secondary               |
| Write concern `w: "majority"` | La write è confermata solo quando la MAGGIORANZA dei nodi l'ha scritta |
| Read preference `secondary`   | Le letture vanno ai secondari per ridurre il carico sul primary        |
| CP                            | Sacrifica la disponibilità per garantire dati sempre corretti          |
| AP                            | Sacrifica la consistenza per garantire che il sistema risponda sempre  |

## Cleanup

Nessun cleanup richiesto — lab teorico.
