# OFDL PD Controller — Användarguide

En linjeföljan­de PD-kontroller (Proportionell-Derivativ) som läser av två färgsensorer och styr två motorer för att hålla roboten centrerad på linjen.

---

## Koncept

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Endast P-reglering** (`P_*`-lägen): använder bara `Kp × error`
- **PD-reglering** (`PD_*`-lägen): använder både `Kp` och `Kd`
- **PDpwr** (`PDpwr_*`-lägen): samma som PD, men läser Power från global konfiguration

---

## Inställning

### Steg 1 — Konfigurationsblock (kör en gång före loopen)

| Parameter | Beskrivning | Typiskt värde |
|-----------|-------------|---------------|
| **Ports** | Motorportar (B+C) | `1.B+C` |
| **Kp** | Proportionell förstärkning | `0.3` |
| **Kd** | Derivativ förstärkning | `0.7` |
| **Power** | Grundläggande motorhastighet (−100 till 100) | `50` |

### Steg 2 — Styrblock (kör vid varje loopcykel)

| Parameter | Beskrivning |
|-----------|-------------|
| **P1** | Råvärde från vänster färgsensor |
| **P2** | Råvärde från höger färgsensor |

---

## Lägen

| Läge | Kategori | Beskrivning |
|------|----------|-------------|
| `Configuration` | — | Ange Kp, Kd, Power, Ports (anropa en gång) |
| `P_Large` | P\_byVars | P-reglering, stora motorportar |
| `P_Medium` | P\_byVars | P-reglering, medelstora motorportar |
| `P_twoRev` | P\_byVars | P-reglering, omvänd motorriktning |
| `PD_Large` | PD\_byVars | PD-reglering, stora motorportar |
| `PD_Medium` | PD\_byVars | PD-reglering, medelstora motorportar |
| `PD_twoRev` | PD\_byVars | PD-reglering, omvänd motorriktning |
| `PDpwr_Large` | PDpwr\_byVars | PD + konfigurerbar effekt, stora motorer |
| `PDpwr_Medium` | PDpwr\_byVars | PD + konfigurerbar effekt, medelstora motorer |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + konfigurerbar effekt, omvänd riktning |

---

## Typisk loopstruktur

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## Tips

- Börja med `Kp=0.3, Kd=0.7`. Öka `Kp` för snabbare korrigering; öka `Kd` för att minska svängningar.
- Använd `PDpwr_*`-lägen när du vill variera hastigheten dynamiskt (t.ex. kombinerat med Acceleration Controller).
- Använd `P_twoRev` / `PD_twoRev` om robotens vänster- och högermotorer är monterade i motsatta riktningar.
