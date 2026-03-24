# OFDL PD Controller — Brukerveiledning

En linjefølgende PD-kontroller (Proporsjonal-Derivativ) som leser to fargesensorer og styrer to motorer for å holde roboten sentrert på linjen.

---

## Konsept

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Kun P-regulering** (`P_*`-moduser): bruker kun `Kp × error`
- **PD-regulering** (`PD_*`-moduser): bruker både `Kp` og `Kd`
- **PDpwr** (`PDpwr_*`-moduser): samme som PD, men leser Power fra global konfigurasjon

---

## Oppsett

### Trinn 1 — Konfigurasjonsblokk (kjør én gang før løkken)

| Parameter | Beskrivelse | Typisk verdi |
|-----------|-------------|--------------|
| **Ports** | Motorporter (B+C) | `1.B+C` |
| **Kp** | Proporsjonal forsterkning | `0.3` |
| **Kd** | Derivativ forsterkning | `0.7` |
| **Power** | Grunnleggende motorhastighet (−100 til 100) | `50` |

### Trinn 2 — Styringsblokk (kjør ved hver løkkeitasjon)

| Parameter | Beskrivelse |
|-----------|-------------|
| **P1** | Rå verdi fra venstre fargesensor |
| **P2** | Rå verdi fra høyre fargesensor |

---

## Moduser

| Modus | Kategori | Beskrivelse |
|-------|----------|-------------|
| `Configuration` | — | Sett Kp, Kd, Power, Ports (kall én gang) |
| `P_Large` | P\_byVars | P-regulering, store motorporter |
| `P_Medium` | P\_byVars | P-regulering, mellomstore motorporter |
| `P_twoRev` | P\_byVars | P-regulering, omvendt motorretning |
| `PD_Large` | PD\_byVars | PD-regulering, store motorporter |
| `PD_Medium` | PD\_byVars | PD-regulering, mellomstore motorporter |
| `PD_twoRev` | PD\_byVars | PD-regulering, omvendt motorretning |
| `PDpwr_Large` | PDpwr\_byVars | PD + konfigurerbar effekt, store motorer |
| `PDpwr_Medium` | PDpwr\_byVars | PD + konfigurerbar effekt, mellomstore motorer |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + konfigurerbar effekt, omvendt retning |

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

- Start med `Kp=0.3, Kd=0.7`. Øk `Kp` for raskere korreksjon; øk `Kd` for å redusere svingninger.
- Bruk `PDpwr_*`-moduser når du vil variere hastigheten dynamisk (f.eks. kombinert med Acceleration Controller).
- Bruk `P_twoRev` / `PD_twoRev` hvis robotens venstre og høyre motorer er montert i motsatte retninger.
