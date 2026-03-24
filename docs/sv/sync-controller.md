# OFDL MoveSync Controller — Användarguide

Håller två motorer igång med samma hastighet under rätlinjig körning genom att kontinuerligt korrigera för encoderdrift med hjälp av en P-kontroller.

---

## Koncept

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Utan synkronisering kommer även identiska motorer vid samma beordrade hastighet att med tiden avvika från varandra på grund av mekaniska skillnader. Det här blocket eliminerar den driften.

---

## Inställning

### Steg 1 — Konfigurationsblock (kör en gång före loopen)

| Parameter | Beskrivning | Typiskt värde |
|-----------|-------------|---------------|
| **Ports** | Motorportar | `1.B+C` |
| **Kp** | Synkroniseringskorrigeringsförstärkning | `0.12` |
| **Power1** | Vänster motors grundhastighet (−100 till 100) | `50` |
| **Power2** | Höger motors grundhastighet (−100 till 100) | `50` |
| **LeftInversed** | Invertera vänster motorriktning | `False` |
| **RightInversed** | Invertera höger motorriktning | `False` |

### Steg 2 — Synkroniseringsblock (kör vid varje loopcykel)

| Parameter | Beskrivning |
|-----------|-------------|
| **E1** | Vänster encoderläsning |
| **E2** | Höger encoderläsning |

---

## Lägen

| Läge | Beskrivning |
|------|-------------|
| `Configuration` | Ange Kp, Power1, Power2, Ports, inverteringsflaggor (anropa en gång) |
| `SYNC_Std` | Synkronisera med konfigurerade Power1/Power2 |
| `SYNC_byPwr` | Synkronisera med Power1/Power2 som skickas vid varje cykel (åsidosätter konfiguration) |

---

## Typisk loopstruktur

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

- Sätt `Power1 = Power2` för rätlinjig körning. Avsiktligt ojämlika värden ger en konsekvent båge.
- Använd `LeftInversed` / `RightInversed` om dina motorer är fysiskt spegelvända (en pekar framåt, en bakåt).
- Öka `Kp` för tätare synkronisering; om roboten svänger, minska den.
- Kombinera med **Acceleration Controller** för att hålla motorerna synkroniserade under acceleration och deceleration: skicka `Output` till `SYNC_byPwr`.
