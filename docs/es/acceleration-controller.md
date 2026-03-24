# OFDL Acceleration Controller — Guía de uso

Genera un perfil de velocidad suave que **sube** de `MinPwr` a `MaxPwr` durante `AccelDist` grados, mantiene `MaxPwr` en el tramo medio, luego **baja** de nuevo a `MinPwr` durante `DecelDist` grados — todo basado en lecturas de encoders en tiempo real.

---

## Concepto

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

El bloque **emite un valor de velocidad** — conéctelo a la entrada de potencia del motor en cada iteración del bucle. No controla los motores directamente.

---

## Configuración

### Paso 1 — Bloque de configuración (ejecutar una vez antes del bucle)

| Parámetro | Descripción | Valor típico |
|-----------|-------------|-------------|
| **EncPort** | Puertos de motor encoders | `1.B+C` |
| **MaxPwr** | Velocidad máxima (−100 a 100) | `90` |
| **MinPwr** | Velocidad mínima al inicio/final (−100 a 100) | `15` |
| **AccelDist** | Distancia de aceleración (grados) | `300` |
| **DecelDist** | Distancia de deceleración (grados) | `300` |
| **FullDist** | Distancia total de recorrido (grados) | `1000` |

### Paso 2 — Bloque de salida (ejecutar en cada iteración del bucle)

#### Salidas

| Salida | Descripción |
|--------|-------------|
| **Output** | Velocidad actual a aplicar a los motores |
| **E1Output** | Lectura actual del encoder 1 (grados) |
| **E2Output** | Lectura actual del encoder 2 (grados) |
| **Finish** | `True` cuando se ha alcanzado `FullDist` |

---

## Modos

| Modo | Descripción |
|------|-------------|
| `Configuration` | Establecer todos los parámetros (llamar una vez antes del bucle) |
| `ACDC_Output1Degs` | Salida de velocidad usando solo el encoder 1 |
| `ACDC_Output2Degs` | Salida de velocidad usando ambos encoders (promediados) |

---

## Estructura de bucle típica

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## Consejos

- Siempre **reinicie los encoders** antes de comenzar (use el bloque Advanced Encoder `EncReset`).
- `AccelDist + DecelDist` debe ser menor que `FullDist`, de lo contrario los perfiles se superponen.
- Combine con el **PD Controller** (modos `PDpwr_*`) para un seguimiento de línea suave a velocidad variable: alimente `Output` como entrada de potencia al bloque PD.
- Use `ACDC_Output1Degs` para tareas de motor único; use `ACDC_Output2Degs` para accionamientos de dos motores para compensar la deriva del encoder.
