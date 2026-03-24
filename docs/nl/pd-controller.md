# OFDL PD Controller — Gebruikershandleiding

Een PD-regelaar (Proportioneel-Differentieel) voor lijnvolgen die twee kleurensensoren leest en twee motoren aanstuurt om de robot gecentreerd op de lijn te houden.

---

## Concept

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Alleen P-regeling** (modi `P_*`): gebruikt alleen `Kp × error`
- **PD-regeling** (modi `PD_*`): gebruikt zowel `Kp` als `Kd`
- **PDpwr** (modi `PDpwr_*`): zelfde als PD, maar leest Power uit de globale configuratie

---

## Instelling

### Stap 1 — Configuratieblok (één keer uitvoeren vóór de lus)

| Parameter | Beschrijving | Typische waarde |
|-----------|-------------|----------------|
| **Ports** | Motorpoorten (B+C) | `1.B+C` |
| **Kp** | Proportionele versterking | `0.3` |
| **Kd** | Differentiële versterking | `0.7` |
| **Power** | Basis motorsnelheid (−100 tot 100) | `50` |

### Stap 2 — Regelblok (bij elke lusiteratie uitvoeren)

| Parameter | Beschrijving |
|-----------|-------------|
| **P1** | Ruwe waarde linker kleurensensor |
| **P2** | Ruwe waarde rechter kleurensensor |

---

## Modi

| Modus | Categorie | Beschrijving |
|-------|-----------|-------------|
| `Configuration` | — | Kp, Kd, Power, Ports instellen (één keer aanroepen) |
| `P_Large` | P\_byVars | P-regeling, grote motorpoorten |
| `P_Medium` | P\_byVars | P-regeling, middelgrote motorpoorten |
| `P_twoRev` | P\_byVars | P-regeling, omgekeerde motorrichting |
| `PD_Large` | PD\_byVars | PD-regeling, grote motorpoorten |
| `PD_Medium` | PD\_byVars | PD-regeling, middelgrote motorpoorten |
| `PD_twoRev` | PD\_byVars | PD-regeling, omgekeerde motorrichting |
| `PDpwr_Large` | PDpwr\_byVars | PD + instelbaar vermogen, grote motoren |
| `PDpwr_Medium` | PDpwr\_byVars | PD + instelbaar vermogen, middelgrote motoren |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + instelbaar vermogen, omgekeerd |

---

## Typische lusstructuur

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## Tips

- Begin met `Kp=0.3, Kd=0.7`. Verhoog `Kp` voor snellere correctie; verhoog `Kd` om oscillaties te verminderen.
- Gebruik `PDpwr_*`-modi wanneer u de snelheid dynamisch wilt variëren (bijv. gecombineerd met de Acceleration Controller).
- Gebruik `P_twoRev` / `PD_twoRev` als de linker- en rechtermotoren van uw robot in tegengestelde richtingen zijn gemonteerd.
