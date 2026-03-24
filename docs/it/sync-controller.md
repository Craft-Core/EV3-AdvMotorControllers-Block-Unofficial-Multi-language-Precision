# OFDL MoveSync Controller — Guida all'uso

Mantiene due motori alla stessa velocità durante il percorso in linea retta correggendo continuamente la deriva degli encoder tramite un controllore P.

---

## Concetto

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Senza sincronizzazione, anche motori identici alla stessa velocità comandata si discosteranno nel tempo a causa di differenze meccaniche. Questo blocco elimina quella deriva.

---

## Configurazione

### Passo 1 — Blocco di configurazione (eseguire una volta prima del ciclo)

| Parametro | Descrizione | Valore tipico |
|-----------|-------------|--------------|
| **Ports** | Porte motore | `1.B+C` |
| **Kp** | Guadagno di correzione sincronizzazione | `0.12` |
| **Power1** | Velocità base motore sinistro (−100 a 100) | `50` |
| **Power2** | Velocità base motore destro (−100 a 100) | `50` |
| **LeftInversed** | Invertire direzione motore sinistro | `False` |
| **RightInversed** | Invertire direzione motore destro | `False` |

### Passo 2 — Blocco di sincronizzazione (eseguire ad ogni iterazione del ciclo)

| Parametro | Descrizione |
|-----------|-------------|
| **E1** | Lettura encoder sinistro |
| **E2** | Lettura encoder destro |

---

## Modalità

| Modalità | Descrizione |
|----------|-------------|
| `Configuration` | Impostare Kp, Power1, Power2, Ports, flag di inversione (chiamare una volta) |
| `SYNC_Std` | Sincronizzazione con Power1/Power2 configurati |
| `SYNC_byPwr` | Sincronizzazione con Power1/Power2 passati ad ogni iterazione (sovrascrive la config) |

---

## Struttura tipica del ciclo

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

Con potenza dinamica:

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## Suggerimenti

- Impostare `Power1 = Power2` per il percorso rettilineo. Valori intenzionalmente diversi produrranno un arco costante.
- Usare `LeftInversed` / `RightInversed` se i motori sono fisicamente speculari (uno rivolto in avanti, l'altro all'indietro).
- Aumentare `Kp` per una sincronizzazione più precisa; se il robot oscilla, ridurlo.
- Combinare con l'**Acceleration Controller** per mantenere i motori sincronizzati durante la rampa di accelerazione e decelerazione: alimentare `Output` in `SYNC_byPwr`.
