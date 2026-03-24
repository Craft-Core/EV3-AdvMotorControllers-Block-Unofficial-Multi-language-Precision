# OFDL Acceleration Controller — Benutzerhandbuch

Erzeugt ein gleichmäßiges Geschwindigkeitsprofil, das über `AccelDist` Grad von `MinPwr` auf `MaxPwr` **hochfährt**, in der Mitte mit `MaxPwr` läuft, dann über `DecelDist` Grad wieder auf `MinPwr` **herunterfährt** — alles basierend auf Live-Encoder-Messwerten.

---

## Konzept

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

Der Block **gibt einen Geschwindigkeitswert aus** — verbinden Sie ihn bei jeder Schleifeniteration mit dem Motorleistungseingang. Er treibt Motoren nicht direkt an.

---

## Einrichtung

### Schritt 1 — Konfigurationsblock (einmal vor der Schleife ausführen)

| Parameter | Beschreibung | Typischer Wert |
|-----------|-------------|----------------|
| **EncPort** | Encoder-Motorports | `1.B+C` |
| **MaxPwr** | Spitzengeschwindigkeit (−100 bis 100) | `90` |
| **MinPwr** | Mindestgeschwindigkeit am Start/Ende (−100 bis 100) | `15` |
| **AccelDist** | Beschleunigungsstrecke (Grad) | `300` |
| **DecelDist** | Bremsstrecke (Grad) | `300` |
| **FullDist** | Gesamtfahrstrecke (Grad) | `1000` |

### Schritt 2 — Ausgabeblock (bei jeder Schleifeniteration ausführen)

#### Ausgaben

| Ausgabe | Beschreibung |
|---------|-------------|
| **Output** | Aktuelle auf die Motoren anzuwendende Geschwindigkeit |
| **E1Output** | Aktueller Encoder-1-Messwert (Grad) |
| **E2Output** | Aktueller Encoder-2-Messwert (Grad) |
| **Finish** | `True`, wenn `FullDist` erreicht wurde |

---

## Modi

| Modus | Beschreibung |
|-------|-------------|
| `Configuration` | Alle Parameter einstellen (einmal vor der Schleife aufrufen) |
| `ACDC_Output1Degs` | Geschwindigkeit nur mit Encoder 1 ausgeben |
| `ACDC_Output2Degs` | Geschwindigkeit mit beiden Encodern ausgeben (gemittelt) |

---

## Typische Schleifenstruktur

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## Tipps

- **Setzen Sie die Encoder** vor dem Start immer zurück (verwenden Sie den Advanced Encoder `EncReset`-Block).
- `AccelDist + DecelDist` muss kleiner als `FullDist` sein, sonst überlappen sich die Profile.
- Kombinieren Sie mit dem **PD Controller** (`PDpwr_*`-Modi) für gleichmäßiges Linienfolgen bei variabler Geschwindigkeit: Führen Sie `Output` als Leistungseingang in den PD-Block.
- Verwenden Sie `ACDC_Output1Degs` für Einzelmotoraufgaben; verwenden Sie `ACDC_Output2Degs` für Zweimotorantriebe, um Encoder-Drift zu berücksichtigen.
