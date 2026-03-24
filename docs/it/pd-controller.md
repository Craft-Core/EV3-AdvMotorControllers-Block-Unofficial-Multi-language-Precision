# OFDL PD Controller — Guida all'uso

Un controllore PD (Proporzionale-Derivativo) per il line following che legge due sensori di colore e pilota due motori per mantenere il robot centrato sulla linea.

---

## Concetto

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Solo controllo P** (modalità `P_*`): usa solo `Kp × error`
- **Controllo PD** (modalità `PD_*`): usa sia `Kp` che `Kd`
- **PDpwr** (modalità `PDpwr_*`): come PD, ma legge Power dalla configurazione globale

---

## Configurazione

### Passo 1 — Blocco di configurazione (eseguire una volta prima del ciclo)

| Parametro | Descrizione | Valore tipico |
|-----------|-------------|--------------|
| **Ports** | Porte motore (B+C) | `1.B+C` |
| **Kp** | Guadagno proporzionale | `0.3` |
| **Kd** | Guadagno derivativo | `0.7` |
| **Power** | Velocità base del motore (−100 a 100) | `50` |

### Passo 2 — Blocco di controllo (eseguire ad ogni iterazione del ciclo)

| Parametro | Descrizione |
|-----------|-------------|
| **P1** | Valore grezzo del sensore di colore sinistro |
| **P2** | Valore grezzo del sensore di colore destro |

---

## Modalità

| Modalità | Categoria | Descrizione |
|----------|-----------|-------------|
| `Configuration` | — | Impostare Kp, Kd, Power, Ports (chiamare una volta) |
| `P_Large` | P\_byVars | Controllo P, porte motore grande |
| `P_Medium` | P\_byVars | Controllo P, porte motore medio |
| `P_twoRev` | P\_byVars | Controllo P, direzione motore invertita |
| `PD_Large` | PD\_byVars | Controllo PD, porte motore grande |
| `PD_Medium` | PD\_byVars | Controllo PD, porte motore medio |
| `PD_twoRev` | PD\_byVars | Controllo PD, direzione motore invertita |
| `PDpwr_Large` | PDpwr\_byVars | PD + potenza configurabile, motori grandi |
| `PDpwr_Medium` | PDpwr\_byVars | PD + potenza configurabile, motori medi |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + potenza configurabile, invertito |

---

## Struttura tipica del ciclo

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## Suggerimenti

- Iniziare con `Kp=0.3, Kd=0.7`. Aumentare `Kp` per una correzione più rapida; aumentare `Kd` per ridurre le oscillazioni.
- Usare le modalità `PDpwr_*` quando si vuole variare la velocità dinamicamente (es. combinato con l'Acceleration Controller).
- Usare `P_twoRev` / `PD_twoRev` se i motori sinistro e destro del robot sono montati in orientamenti opposti.
