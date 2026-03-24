# OFDL PD Controller — Brugervejledning

En linjeføgende PD-controller (Proportional-Derivativ), der aflæser to farve­sensorer og styrer to motorer for at holde robotten centreret på linjen.

---

## Koncept

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Kun P-regulering** (`P_*`-tilstande): bruger kun `Kp × error`
- **PD-regulering** (`PD_*`-tilstande): bruger både `Kp` og `Kd`
- **PDpwr** (`PDpwr_*`-tilstande): samme som PD, men læser Power fra global konfiguration

---

## Opsætning

### Trin 1 — Konfigurationsblok (kør én gang før løkken)

| Parameter | Beskrivelse | Typisk værdi |
|-----------|-------------|--------------|
| **Ports** | Motorporte (B+C) | `1.B+C` |
| **Kp** | Proportional forstærkning | `0.3` |
| **Kd** | Derivativ forstærkning | `0.7` |
| **Power** | Grundlæggende motorhastighed (−100 til 100) | `50` |

### Trin 2 — Styringsblok (kør ved hver løkkeiteration)

| Parameter | Beskrivelse |
|-----------|-------------|
| **P1** | Rå værdi fra venstre farve­sensor |
| **P2** | Rå værdi fra højre farve­sensor |

---

## Tilstande

| Tilstand | Kategori | Beskrivelse |
|----------|----------|-------------|
| `Configuration` | — | Sæt Kp, Kd, Power, Ports (kald én gang) |
| `P_Large` | P\_byVars | P-regulering, store motorporte |
| `P_Medium` | P\_byVars | P-regulering, mellemstore motorporte |
| `P_twoRev` | P\_byVars | P-regulering, omvendt motorretning |
| `PD_Large` | PD\_byVars | PD-regulering, store motorporte |
| `PD_Medium` | PD\_byVars | PD-regulering, mellemstore motorporte |
| `PD_twoRev` | PD\_byVars | PD-regulering, omvendt motorretning |
| `PDpwr_Large` | PDpwr\_byVars | PD + justerbar effekt, store motorer |
| `PDpwr_Medium` | PDpwr\_byVars | PD + justerbar effekt, mellemstore motorer |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + justerbar effekt, omvendt retning |

---

## Typisk løkkestruktur

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## Tips

- Start med `Kp=0.3, Kd=0.7`. Øg `Kp` for hurtigere korrektion; øg `Kd` for at reducere svingninger.
- Brug `PDpwr_*`-tilstande, når du vil variere hastigheden dynamisk (f.eks. kombineret med Acceleration Controller).
- Brug `P_twoRev` / `PD_twoRev`, hvis robotens venstre og højre motorer er monteret i modsatte retninger.
