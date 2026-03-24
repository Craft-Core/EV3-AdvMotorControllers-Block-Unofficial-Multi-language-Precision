# OFDL MoveSync Controller — Brukerveiledning

Holder to motorer i gang ved samme hastighet under rettlinjet kjøring ved å kontinuerlig korrigere for encoderdrift ved hjelp av en P-kontroller.

---

## Konsept

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Uten synkronisering vil selv identiske motorer ved samme beordrede hastighet over tid avvike fra hverandre på grunn av mekaniske forskjeller. Denne blokken eliminerer den driften.

---

## Oppsett

### Trinn 1 — Konfigurasjonsblokk (kjør én gang før løkken)

| Parameter | Beskrivelse | Typisk verdi |
|-----------|-------------|--------------|
| **Ports** | Motorporter | `1.B+C` |
| **Kp** | Synkroniseringskorreksjonforsterkning | `0.12` |
| **Power1** | Venstre motors grunnhastighet (−100 til 100) | `50` |
| **Power2** | Høyre motors grunnhastighet (−100 til 100) | `50` |
| **LeftInversed** | Inverter venstre motorretning | `False` |
| **RightInversed** | Inverter høyre motorretning | `False` |

### Trinn 2 — Synkroniseringsblokk (kjør ved hver løkkeitasjon)

| Parameter | Beskrivelse |
|-----------|-------------|
| **E1** | Venstre encoderavlesning |
| **E2** | Høyre encoderavlesning |

---

## Moduser

| Modus | Beskrivelse |
|-------|-------------|
| `Configuration` | Sett Kp, Power1, Power2, Ports, inversjons­flagg (kall én gang) |
| `SYNC_Std` | Synkroniser med konfigurerte Power1/Power2 |
| `SYNC_byPwr` | Synkroniser med Power1/Power2 sendt ved hver itasjon (overstyrer konfigurasjon) |

---

## Typisk løkkestruktur

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

Eller med dynamisk effekt:

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## Tips

- Sett `Power1 = Power2` for rettlinjet kjøring. Bevisst ulike verdier vil produsere en konsistent bue.
- Bruk `LeftInversed` / `RightInversed` hvis motorene dine er fysisk speilvendte (en vender fremover, en bakover).
- Øk `Kp` for tettere synkronisering; hvis roboten svinger, reduser den.
- Kombiner med **Acceleration Controller** for å holde motorene synkronisert under akselerasjon og deselearasjon: send `Output` til `SYNC_byPwr`.
