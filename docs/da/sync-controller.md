# OFDL MoveSync Controller — Brugervejledning

Holder to motorer kørende ved samme hastighed under retlinjet kørsel ved kontinuerligt at korrigere for encoderdrift via en P-controller.

---

## Koncept

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Uden synkronisering vil selv identiske motorer ved samme beordrede hastighed med tiden afvige fra hinanden på grund af mekaniske forskelle. Denne blok eliminerer den drift.

---

## Opsætning

### Trin 1 — Konfigurationsblok (kør én gang før løkken)

| Parameter | Beskrivelse | Typisk værdi |
|-----------|-------------|--------------|
| **Ports** | Motorporte | `1.B+C` |
| **Kp** | Synkroniseringskorrektionsforstærkning | `0.12` |
| **Power1** | Venstre motors grundhastighed (−100 til 100) | `50` |
| **Power2** | Højre motors grundhastighed (−100 til 100) | `50` |
| **LeftInversed** | Inverter venstre motorretning | `False` |
| **RightInversed** | Inverter højre motorretning | `False` |

### Trin 2 — Synkroniseringsblok (kør ved hver løkkeiteration)

| Parameter | Beskrivelse |
|-----------|-------------|
| **E1** | Venstre encoderaflæsning |
| **E2** | Højre encoderaflæsning |

---

## Tilstande

| Tilstand | Beskrivelse |
|----------|-------------|
| `Configuration` | Sæt Kp, Power1, Power2, Ports, inversions­flag (kald én gang) |
| `SYNC_Std` | Synkroniser med konfigurerede Power1/Power2 |
| `SYNC_byPwr` | Synkroniser med Power1/Power2 sendt ved hver iteration (tilsidesætter konfiguration) |

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

- Sæt `Power1 = Power2` til retlinjet kørsel. Bevidst ulige værdier vil producere en ensartet bue.
- Brug `LeftInversed` / `RightInversed`, hvis dine motorer er fysisk spejlvendte (en vender fremad, en bagud).
- Øg `Kp` for tættere synkronisering; hvis robotten svinger, reducér den.
- Kombiner med **Acceleration Controller** for at holde motorerne synkroniserede under acceleration og deceleration: send `Output` til `SYNC_byPwr`.
