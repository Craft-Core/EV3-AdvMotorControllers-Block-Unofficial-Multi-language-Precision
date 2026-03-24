# OFDL PD Controller — Benutzerhandbuch

Ein PD-Regler (Proportional-Differential) für die Linienfolge, der zwei Farbsensoren liest und zwei Motoren antreibt, um den Roboter auf der Linie zu halten.

---

## Konzept

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Nur P-Regler** (`P_*`-Modi): verwendet nur `Kp × error`
- **PD-Regler** (`PD_*`-Modi): verwendet sowohl `Kp` als auch `Kd`
- **PDpwr** (`PDpwr_*`-Modi): wie PD, liest aber Power aus der globalen Konfiguration

---

## Einrichtung

### Schritt 1 — Konfigurationsblock (einmal vor der Schleife ausführen)

| Parameter | Beschreibung | Typischer Wert |
|-----------|-------------|----------------|
| **Ports** | Motorports (B+C) | `1.B+C` |
| **Kp** | Proportionalverstärkung | `0.3` |
| **Kd** | Differentialverstärkung | `0.7` |
| **Power** | Basis-Motorgeschwindigkeit (−100 bis 100) | `50` |

### Schritt 2 — Regelblock (bei jeder Schleifeniteration ausführen)

| Parameter | Beschreibung |
|-----------|-------------|
| **P1** | Rohwert des linken Farbsensors |
| **P2** | Rohwert des rechten Farbsensors |

---

## Modi

| Modus | Kategorie | Beschreibung |
|-------|-----------|-------------|
| `Configuration` | — | Kp, Kd, Power, Ports einstellen (einmal aufrufen) |
| `P_Large` | P\_byVars | P-Regler, große Motorports |
| `P_Medium` | P\_byVars | P-Regler, mittlere Motorports |
| `P_twoRev` | P\_byVars | P-Regler, umgekehrte Motorrichtung |
| `PD_Large` | PD\_byVars | PD-Regler, große Motorports |
| `PD_Medium` | PD\_byVars | PD-Regler, mittlere Motorports |
| `PD_twoRev` | PD\_byVars | PD-Regler, umgekehrte Motorrichtung |
| `PDpwr_Large` | PDpwr\_byVars | PD + konfigurierbares Power, große Motoren |
| `PDpwr_Medium` | PDpwr\_byVars | PD + konfigurierbares Power, mittlere Motoren |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + konfigurierbares Power, umgekehrt |

---

## Typische Schleifenstruktur

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## Tipps

- Beginnen Sie mit `Kp=0.3, Kd=0.7`. Erhöhen Sie `Kp` für schnellere Korrekturen; erhöhen Sie `Kd`, um Schwingungen zu reduzieren.
- Verwenden Sie `PDpwr_*`-Modi, wenn Sie die Geschwindigkeit dynamisch variieren möchten (z. B. in Kombination mit dem Acceleration Controller).
- Verwenden Sie `P_twoRev` / `PD_twoRev`, wenn die linken und rechten Motoren Ihres Roboters in entgegengesetzter Ausrichtung montiert sind.
