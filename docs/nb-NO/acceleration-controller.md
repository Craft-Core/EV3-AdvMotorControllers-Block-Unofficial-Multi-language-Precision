# OFDL Acceleration Controller — Brukerveiledning

Genererer en jevn hastighetsprofil som akselererer **opp** fra `MinPwr` til `MaxPwr` over `AccelDist` grader, kjører ved `MaxPwr` i midten og deretter deselererer **ned** tilbake til `MinPwr` over `DecelDist` grader — alt basert på direkteafleste encoderverdier.

---

## Konsept

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

Blokken **gir ut en hastighetsverdi** — koble den til motorens effektinngang ved hver løkkeitasjon. Den styrer ikke motorer direkte.

---

## Oppsett

### Trinn 1 — Konfigurasjonsblokk (kjør én gang før løkken)

| Parameter | Beskrivelse | Typisk verdi |
|-----------|-------------|--------------|
| **EncPort** | Encodermotorporter | `1.B+C` |
| **MaxPwr** | Toppfart (−100 til 100) | `90` |
| **MinPwr** | Minimumshastighet ved start/slutt (−100 til 100) | `15` |
| **AccelDist** | Akselerasjonsavstand (grader) | `300` |
| **DecelDist** | Deselerasjonsavstand (grader) | `300` |
| **FullDist** | Total kjørestrekning (grader) | `1000` |

### Trinn 2 — Utgangsblokk (kjør ved hver løkkeitasjon)

#### Utganger

| Utgang | Beskrivelse |
|--------|-------------|
| **Output** | Gjeldende hastighet som skal brukes på motorene |
| **E1Output** | Gjeldende encoder 1-avlesning (grader) |
| **E2Output** | Gjeldende encoder 2-avlesning (grader) |
| **Finish** | `True` når `FullDist` er nådd |

---

## Moduser

| Modus | Beskrivelse |
|-------|-------------|
| `Configuration` | Sett alle parametre (kall én gang før løkken) |
| `ACDC_Output1Degs` | Utgangshastig­het ved bruk av encoder 1 alene |
| `ACDC_Output2Degs` | Utgangshastighet ved bruk av begge encodere (gjennomsnitt) |

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

- **Nullstill alltid encodere** før start (bruk Advanced Encoder `EncReset`-blokken).
- `AccelDist + DecelDist` må være mindre enn `FullDist`, ellers overlapper profilene.
- Kombiner med **PD Controller** (`PDpwr_*`-moduser) for jevn linjefølging ved variabel hastighet: send `Output` som effektinngang til PD-blokken.
- Bruk `ACDC_Output1Degs` for enkeltmotoroppgaver; bruk `ACDC_Output2Degs` for todrevne motorer for å kompensere for encoderdrift.
