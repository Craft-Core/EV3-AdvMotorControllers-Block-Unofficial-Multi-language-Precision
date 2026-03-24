# OFDL Acceleration Controller — Användarguide

Genererar en jämn hastighetsprofil som accelererar **upp** från `MinPwr` till `MaxPwr` över `AccelDist` grader, kör vid `MaxPwr` i mitten och sedan decelererar **ned** tillbaka till `MinPwr` över `DecelDist` grader — allt baserat på live-encoderläsningar.

---

## Koncept

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

Blocket **ger ett hastighetsvärde** — koppla det till motorns effektingång vid varje loopcykel. Det styr inte motorer direkt.

---

## Inställning

### Steg 1 — Konfigurationsblock (kör en gång före loopen)

| Parameter | Beskrivning | Typiskt värde |
|-----------|-------------|---------------|
| **EncPort** | Encodermotorportar | `1.B+C` |
| **MaxPwr** | Toppfart (−100 till 100) | `90` |
| **MinPwr** | Minimihastighet vid start/slut (−100 till 100) | `15` |
| **AccelDist** | Accelerationsavstånd (grader) | `300` |
| **DecelDist** | Decelerationsavstånd (grader) | `300` |
| **FullDist** | Total körsträcka (grader) | `1000` |

### Steg 2 — Utdatablock (kör vid varje loopcykel)

#### Utdata

| Utdata | Beskrivning |
|--------|-------------|
| **Output** | Aktuell hastighet att applicera på motorerna |
| **E1Output** | Aktuell encoder 1-avläsning (grader) |
| **E2Output** | Aktuell encoder 2-avläsning (grader) |
| **Finish** | `True` när `FullDist` har nåtts |

---

## Lägen

| Läge | Beskrivning |
|------|-------------|
| `Configuration` | Ange alla parametrar (anropa en gång före loopen) |
| `ACDC_Output1Degs` | Mata ut hastighet med encoder 1 enbart |
| `ACDC_Output2Degs` | Mata ut hastighet med båda encoders (medelvärde) |

---

## Typisk loopstruktur

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

- **Återställ alltid encoders** innan start (använd Advanced Encoder `EncReset`-blocket).
- `AccelDist + DecelDist` måste vara mindre än `FullDist`, annars överlappar profilerna.
- Kombinera med **PD Controller** (`PDpwr_*`-lägen) för jämn linjeföljning vid variabel hastighet: skicka `Output` som effektingång till PD-blocket.
- Använd `ACDC_Output1Degs` för enmotoruppgifter; använd `ACDC_Output2Degs` för tvåmotordrifter för att kompensera för encoderdrift.
