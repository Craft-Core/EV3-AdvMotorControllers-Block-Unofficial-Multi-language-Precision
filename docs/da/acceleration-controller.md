# OFDL Acceleration Controller — Brugervejledning

Genererer en jævn hastighedsprofil, der accelererer **op** fra `MinPwr` til `MaxPwr` over `AccelDist` grader, kører ved `MaxPwr` i midten og derefter decelererer **ned** til `MinPwr` over `DecelDist` grader — alt baseret på direkte encoderaflæsninger.

---

## Koncept

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

Blokken **udgiver en hastighedsværdi** — forbind den til din motors effektindgang ved hver løkkeiteration. Den styrer ikke motorer direkte.

---

## Opsætning

### Trin 1 — Konfigurationsblok (kør én gang før løkken)

| Parameter | Beskrivelse | Typisk værdi |
|-----------|-------------|--------------|
| **EncPort** | Encodermotor­porte | `1.B+C` |
| **MaxPwr** | Tophastighed (−100 til 100) | `90` |
| **MinPwr** | Minimumshastighed ved start/slut (−100 til 100) | `15` |
| **AccelDist** | Accelerationsafstand (grader) | `300` |
| **DecelDist** | Decelerationsafstand (grader) | `300` |
| **FullDist** | Samlet rejseafstand (grader) | `1000` |

### Trin 2 — Outputblok (kør ved hver løkkeiteration)

#### Output

| Output | Beskrivelse |
|--------|-------------|
| **Output** | Nuværende hastighed til motorerne |
| **E1Output** | Nuværende encoder 1-aflæsning (grader) |
| **E2Output** | Nuværende encoder 2-aflæsning (grader) |
| **Finish** | `True`, når `FullDist` er nået |

---

## Tilstande

| Tilstand | Beskrivelse |
|----------|-------------|
| `Configuration` | Sæt alle parametre (kald én gang før løkken) |
| `ACDC_Output1Degs` | Output hastighed ved brug af encoder 1 alene |
| `ACDC_Output2Degs` | Output hastighed ved brug af begge encodere (gennemsnit) |

---

## Typisk løkkestruktur

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

- **Nulstil altid encodere** før start (brug Advanced Encoder `EncReset`-blokken).
- `AccelDist + DecelDist` skal være mindre end `FullDist`, ellers overlapper profilerne.
- Kombiner med **PD Controller** (`PDpwr_*`-tilstande) til jævn linjeføgning ved variabel hastighed: send `Output` som effektindgang til PD-blokken.
- Brug `ACDC_Output1Degs` til enkeltmotoropgaver; brug `ACDC_Output2Degs` til todrevne motorer for at tage højde for encoderdrift.
