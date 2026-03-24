# OFDL MoveSync Controller — Benutzerhandbuch

Hält zwei Motoren während der Geradeausfahrt auf gleicher Geschwindigkeit, indem der Encoder-Drift mithilfe eines P-Reglers kontinuierlich korrigiert wird.

---

## Konzept

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Ohne Synchronisierung driften selbst identische Motoren bei gleicher Sollgeschwindigkeit mit der Zeit aufgrund mechanischer Unterschiede auseinander. Dieser Block beseitigt diesen Drift.

---

## Einrichtung

### Schritt 1 — Konfigurationsblock (einmal vor der Schleife ausführen)

| Parameter | Beschreibung | Typischer Wert |
|-----------|-------------|----------------|
| **Ports** | Motorports | `1.B+C` |
| **Kp** | Synchronisierungskorrekturgain | `0.12` |
| **Power1** | Basisgeschwindigkeit linker Motor (−100 bis 100) | `50` |
| **Power2** | Basisgeschwindigkeit rechter Motor (−100 bis 100) | `50` |
| **LeftInversed** | Linke Motorrichtung umkehren | `False` |
| **RightInversed** | Rechte Motorrichtung umkehren | `False` |

### Schritt 2 — Synchronisierungsblock (bei jeder Schleifeniteration ausführen)

| Parameter | Beschreibung |
|-----------|-------------|
| **E1** | Messwert des linken Encoders |
| **E2** | Messwert des rechten Encoders |

---

## Modi

| Modus | Beschreibung |
|-------|-------------|
| `Configuration` | Kp, Power1, Power2, Ports, Umkehr-Flags einstellen (einmal aufrufen) |
| `SYNC_Std` | Synchronisierung mit konfiguriertem Power1/Power2 |
| `SYNC_byPwr` | Synchronisierung mit pro Iteration übergebenem Power1/Power2 (überschreibt Konfiguration) |

---

## Typische Schleifenstruktur

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

Mit dynamischer Leistung:

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## Tipps

- Setzen Sie `Power1 = Power2` für Geradeausfahrt. Absichtlich ungleiche Werte erzeugen einen gleichmäßigen Bogen.
- Verwenden Sie `LeftInversed` / `RightInversed`, wenn Ihre Motoren physisch gespiegelt montiert sind (einer zeigt nach vorne, einer nach hinten).
- Erhöhen Sie `Kp` für engere Synchronisierung; wenn der Roboter oszilliert, reduzieren Sie es.
- Kombinieren Sie mit dem **Acceleration Controller**, um Motoren während Hoch- und Runterfahren synchron zu halten: Führen Sie `Output` in `SYNC_byPwr`.
