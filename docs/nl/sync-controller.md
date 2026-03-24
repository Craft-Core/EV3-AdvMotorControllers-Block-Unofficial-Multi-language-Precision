# OFDL MoveSync Controller — Gebruikershandleiding

Houdt twee motoren op dezelfde snelheid tijdens rechtlijnig rijden door encoderdrift continu te corrigeren met een P-regelaar.

---

## Concept

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Zonder synchronisatie zullen zelfs identieke motoren op dezelfde ingestelde snelheid met de tijd uiteen lopen door mechanische verschillen. Dit blok elimineert die drift.

---

## Instelling

### Stap 1 — Configuratieblok (één keer uitvoeren vóór de lus)

| Parameter | Beschrijving | Typische waarde |
|-----------|-------------|----------------|
| **Ports** | Motorpoorten | `1.B+C` |
| **Kp** | Synchronisatiecorrectieversterking | `0.12` |
| **Power1** | Basissnelheid linkermotor (−100 tot 100) | `50` |
| **Power2** | Basissnelheid rechtermotor (−100 tot 100) | `50` |
| **LeftInversed** | Linkermotor richting omkeren | `False` |
| **RightInversed** | Rechtermotor richting omkeren | `False` |

### Stap 2 — Synchronisatieblok (bij elke lusiteratie uitvoeren)

| Parameter | Beschrijving |
|-----------|-------------|
| **E1** | Meting linker encoder |
| **E2** | Meting rechter encoder |

---

## Modi

| Modus | Beschrijving |
|-------|-------------|
| `Configuration` | Kp, Power1, Power2, Ports, inversiemarkeringen instellen (één keer aanroepen) |
| `SYNC_Std` | Synchronisatie met geconfigureerde Power1/Power2 |
| `SYNC_byPwr` | Synchronisatie met Power1/Power2 per iteratie meegegeven (overschrijft configuratie) |

---

## Typische lusstructuur

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

Met dynamisch vermogen:

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## Tips

- Stel `Power1 = Power2` in voor rechtlijnig rijden. Opzettelijk ongelijke waarden produceren een consistente boog.
- Gebruik `LeftInversed` / `RightInversed` als uw motoren fysiek gespiegeld zijn gemonteerd (de ene naar voren, de andere naar achteren).
- Verhoog `Kp` voor nauwere synchronisatie; als de robot oscilleert, verlaag het dan.
- Combineer met de **Acceleration Controller** om motoren gesynchroniseerd te houden tijdens op- en afremmen: voer `Output` in `SYNC_byPwr`.
