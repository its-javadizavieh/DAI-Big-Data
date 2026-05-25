# Soluzioni Lab 03 — Partizionamento, replicazione, fault tolerance

## Esercizio 1: Strategia di partizionamento

| Dataset               | Strategia           | Perché?                                                                                          |
| --------------------- | ------------------- | ------------------------------------------------------------------------------------------------ |
| **A** (ascolti, 8 GB) | **Hash su user_id** | Query frequente: "tutti gli ascolti di un utente" → dati dello stesso utente sullo stesso helper |
| **B** (utenti, 1 GB)  | **Hash su user_id** | Piccolo, ma il join con Dataset A è più efficiente se la stessa chiave sta sullo stesso nodo     |
| **C** (canzoni, 2 GB) | **Round-robin**     | Nessuna query specifica per chiave; distribuzione uniforme è sufficiente                         |

## Esercizio 2: Calcolo spazio con replicazione

Fattore di replicazione = 3 (copie)

```
Dataset A:  8 GB × 3 = 24 GB
Dataset B:  1 GB × 3 =  3 GB
Dataset C:  2 GB × 3 =  6 GB
─────────────────────────────
Totale:    11 GB × 3 = 33 GB
```

Servono **33 GB** di spazio totale nel cluster (divisi tra i 4 helper).

## Esercizio 3: Problema dello skew

**Dataset più a rischio**: Dataset B (utenti), se partizionato **per città** (range).

- Roma ha ~2.8 milioni di abitanti, Aosta ~34.000 → distribuzione **100× sbilanciata**
- L'helper che riceve Roma è sovraccarico, quello con Aosta è quasi vuoto
- Il lavoro complessivo è bloccato dall'helper più lento (_straggler_)

**Soluzione**: Usare **hash partitioning** su `user_id` → distribuzione uniforme indipendentemente dalla città.

## Esercizio 4: Simulazione guasto (Helper 2)

1. **Passo 1**: Il boss nota che l'Helper 2 non manda più heartbeat → lo marca come _down_
2. **Passo 2**: Il boss controlla dove sono le copie (replica) dei dati dell'Helper 2 → le trova su Helper 1 e Helper 4
3. **Passo 3**: Il boss ri-assegna i compiti: Helper 1 processa la copia del Dataset A, Helper 4 la copia del Dataset C
4. **Passo 4**: Il lavoro continua senza ricominciare da zero. Solo le partizioni dell'Helper 2 vengono ricalcolate
