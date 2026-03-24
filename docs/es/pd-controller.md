# OFDL PD Controller — Guía de uso

Un controlador PD (Proporcional-Derivativo) de seguimiento de línea que lee dos sensores de color y controla dos motores para mantener el robot centrado sobre la línea.

---

## Concepto

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Solo control P** (modos `P_*`): usa únicamente `Kp × error`
- **Control PD** (modos `PD_*`): usa tanto `Kp` como `Kd`
- **PDpwr** (modos `PDpwr_*`): igual que PD, pero lee Power desde la configuración global

---

## Configuración

### Paso 1 — Bloque de configuración (ejecutar una vez antes del bucle)

| Parámetro | Descripción | Valor típico |
|-----------|-------------|-------------|
| **Ports** | Puertos de motor (B+C) | `1.B+C` |
| **Kp** | Ganancia proporcional | `0.3` |
| **Kd** | Ganancia derivativa | `0.7` |
| **Power** | Velocidad base del motor (−100 a 100) | `50` |

### Paso 2 — Bloque de control (ejecutar en cada iteración del bucle)

| Parámetro | Descripción |
|-----------|-------------|
| **P1** | Valor bruto del sensor de color izquierdo |
| **P2** | Valor bruto del sensor de color derecho |

---

## Modos

| Modo | Categoría | Descripción |
|------|-----------|-------------|
| `Configuration` | — | Establecer Kp, Kd, Power, Ports (llamar una vez) |
| `P_Large` | P\_byVars | Control P, puertos de motor grande |
| `P_Medium` | P\_byVars | Control P, puertos de motor mediano |
| `P_twoRev` | P\_byVars | Control P, dirección de motor invertida |
| `PD_Large` | PD\_byVars | Control PD, puertos de motor grande |
| `PD_Medium` | PD\_byVars | Control PD, puertos de motor mediano |
| `PD_twoRev` | PD\_byVars | Control PD, dirección de motor invertida |
| `PDpwr_Large` | PDpwr\_byVars | PD + potencia configurable, motores grandes |
| `PDpwr_Medium` | PDpwr\_byVars | PD + potencia configurable, motores medianos |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + potencia configurable, invertido |

---

## Estructura de bucle típica

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## Consejos

- Comience con `Kp=0.3, Kd=0.7`. Aumente `Kp` para una corrección más rápida; aumente `Kd` para reducir las oscilaciones.
- Use los modos `PDpwr_*` cuando desee variar la velocidad dinámicamente (por ejemplo, combinado con el Acceleration Controller).
- Use `P_twoRev` / `PD_twoRev` si los motores izquierdo y derecho de su robot están montados en orientaciones opuestas.
