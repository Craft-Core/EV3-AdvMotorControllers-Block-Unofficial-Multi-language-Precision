# OFDL MoveSync Controller — Guía de uso

Mantiene dos motores a la misma velocidad durante el desplazamiento en línea recta corrigiendo continuamente la deriva del encoder mediante un controlador P.

---

## Concepto

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Sin sincronización, incluso motores idénticos a la misma velocidad comandada se desviarán con el tiempo debido a diferencias mecánicas. Este bloque elimina esa deriva.

---

## Configuración

### Paso 1 — Bloque de configuración (ejecutar una vez antes del bucle)

| Parámetro | Descripción | Valor típico |
|-----------|-------------|-------------|
| **Ports** | Puertos de motor | `1.B+C` |
| **Kp** | Ganancia de corrección de sincronización | `0.12` |
| **Power1** | Velocidad base del motor izquierdo (−100 a 100) | `50` |
| **Power2** | Velocidad base del motor derecho (−100 a 100) | `50` |
| **LeftInversed** | Invertir dirección del motor izquierdo | `False` |
| **RightInversed** | Invertir dirección del motor derecho | `False` |

### Paso 2 — Bloque de sincronización (ejecutar en cada iteración del bucle)

| Parámetro | Descripción |
|-----------|-------------|
| **E1** | Lectura del encoder izquierdo |
| **E2** | Lectura del encoder derecho |

---

## Modos

| Modo | Descripción |
|------|-------------|
| `Configuration` | Establecer Kp, Power1, Power2, Ports, indicadores de inversión (llamar una vez) |
| `SYNC_Std` | Sincronización usando Power1/Power2 configurados |
| `SYNC_byPwr` | Sincronización con Power1/Power2 pasados en cada iteración (anula la config) |

---

## Estructura de bucle típica

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

Con potencia dinámica:

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## Consejos

- Establezca `Power1 = Power2` para desplazamiento recto. Valores intencionalmente desiguales producirán un arco consistente.
- Use `LeftInversed` / `RightInversed` si sus motores están físicamente en espejo (uno mira hacia adelante, el otro hacia atrás).
- Aumente `Kp` para una sincronización más estricta; si el robot oscila, redúzcalo.
- Combine con el **Acceleration Controller** para mantener los motores sincronizados durante la aceleración y desaceleración: alimente `Output` en `SYNC_byPwr`.
