# OFDL Acceleration Controller — Guida all'uso

Genera un profilo di velocità fluido che **sale** da `MinPwr` a `MaxPwr` in `AccelDist` gradi, mantiene `MaxPwr` nel tratto centrale, poi **scende** di nuovo a `MinPwr` in `DecelDist` gradi — tutto basato su letture live degli encoder.

---

## Concetto

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

Il blocco **produce un valore di velocità** — collegarlo all'ingresso di potenza del motore ad ogni iterazione del ciclo. Non pilota i motori direttamente.

---

## Configurazione

### Passo 1 — Blocco di configurazione (eseguire una volta prima del ciclo)

| Parametro | Descrizione | Valore tipico |
|-----------|-------------|--------------|
| **EncPort** | Porte motore encoder | `1.B+C` |
| **MaxPwr** | Velocità massima (−100 a 100) | `90` |
| **MinPwr** | Velocità minima all'inizio/fine (−100 a 100) | `15` |
| **AccelDist** | Distanza di accelerazione (gradi) | `300` |
| **DecelDist** | Distanza di decelerazione (gradi) | `300` |
| **FullDist** | Distanza totale di percorrenza (gradi) | `1000` |

### Passo 2 — Blocco di uscita (eseguire ad ogni iterazione del ciclo)

#### Uscite

| Uscita | Descrizione |
|--------|-------------|
| **Output** | Velocità corrente da applicare ai motori |
| **E1Output** | Lettura corrente dell'encoder 1 (gradi) |
| **E2Output** | Lettura corrente dell'encoder 2 (gradi) |
| **Finish** | `True` quando `FullDist` è stato raggiunto |

---

## Modalità

| Modalità | Descrizione |
|----------|-------------|
| `Configuration` | Impostare tutti i parametri (chiamare una volta prima del ciclo) |
| `ACDC_Output1Degs` | Uscita velocità usando solo l'encoder 1 |
| `ACDC_Output2Degs` | Uscita velocità usando entrambi gli encoder (media) |

---

## Struttura tipica del ciclo

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## Suggerimenti

- **Resettare sempre gli encoder** prima di iniziare (usare il blocco Advanced Encoder `EncReset`).
- `AccelDist + DecelDist` deve essere inferiore a `FullDist`, altrimenti i profili si sovrappongono.
- Combinare con il **PD Controller** (modalità `PDpwr_*`) per un line following fluido a velocità variabile: alimentare `Output` come ingresso di potenza nel blocco PD.
- Usare `ACDC_Output1Degs` per compiti a motore singolo; usare `ACDC_Output2Degs` per azionamenti a due motori per compensare la deriva degli encoder.
