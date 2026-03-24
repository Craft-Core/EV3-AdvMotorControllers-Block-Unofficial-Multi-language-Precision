# OFDL Acceleration Controller — Gebruikershandleiding

Genereert een vloeiend snelheidsprofiel dat **omhoog** gaat van `MinPwr` naar `MaxPwr` over `AccelDist` graden, op `MaxPwr` rijdt door het midden, dan **omlaag** terugkeert naar `MinPwr` over `DecelDist` graden — allemaal gebaseerd op live encodermetingen.

---

## Concept

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

Het blok **geeft een snelheidswaarde uit** — verbind deze bij elke lusiteratie met de motorvermogensingang. Het stuurt motoren niet rechtstreeks aan.

---

## Instelling

### Stap 1 — Configuratieblok (één keer uitvoeren vóór de lus)

| Parameter | Beschrijving | Typische waarde |
|-----------|-------------|----------------|
| **EncPort** | Encodermotorpoorten | `1.B+C` |
| **MaxPwr** | Maximale snelheid (−100 tot 100) | `90` |
| **MinPwr** | Minimale snelheid bij start/einde (−100 tot 100) | `15` |
| **AccelDist** | Versnellingsafstand (graden) | `300` |
| **DecelDist** | Afremafstand (graden) | `300` |
| **FullDist** | Totale reisafstand (graden) | `1000` |

### Stap 2 — Uitvoerblok (bij elke lusiteratie uitvoeren)

#### Uitgangen

| Uitgang | Beschrijving |
|---------|-------------|
| **Output** | Huidige snelheid toe te passen op motoren |
| **E1Output** | Huidige encoder 1-meting (graden) |
| **E2Output** | Huidige encoder 2-meting (graden) |
| **Finish** | `True` wanneer `FullDist` is bereikt |

---

## Modi

| Modus | Beschrijving |
|-------|-------------|
| `Configuration` | Alle parameters instellen (één keer vóór de lus aanroepen) |
| `ACDC_Output1Degs` | Snelheid uitvoeren met alleen encoder 1 |
| `ACDC_Output2Degs` | Snelheid uitvoeren met beide encoders (gemiddeld) |

---

## Typische lusstructuur

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## Tips

- **Reset encoders** altijd vóór het starten (gebruik het Advanced Encoder `EncReset`-blok).
- `AccelDist + DecelDist` moet kleiner zijn dan `FullDist`, anders overlappen de profielen.
- Combineer met de **PD Controller** (modi `PDpwr_*`) voor vloeiend lijnvolgen op variabele snelheid: voer `Output` in als vermogensingang naar het PD-blok.
- Gebruik `ACDC_Output1Degs` voor enkelmotortaken; gebruik `ACDC_Output2Degs` voor tweeMotoren-aandrijvingen om encoderdrift te compenseren.
