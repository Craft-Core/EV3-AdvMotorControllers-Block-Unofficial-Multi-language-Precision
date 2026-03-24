# OFDL PD Controller — Guia de uso

Um controlador PD (Proporcional-Derivativo) de seguimento de linha que lê dois sensores de cor e aciona dois motores para manter o robô centralizado na linha.

---

## Conceito

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Apenas controle P** (modos `P_*`): usa somente `Kp × error`
- **Controle PD** (modos `PD_*`): usa tanto `Kp` quanto `Kd`
- **PDpwr** (modos `PDpwr_*`): igual ao PD, mas lê Power da configuração global

---

## Configuração

### Passo 1 — Bloco de configuração (executar uma vez antes do laço)

| Parâmetro | Descrição | Valor típico |
|-----------|-----------|-------------|
| **Ports** | Portas dos motores (B+C) | `1.B+C` |
| **Kp** | Ganho proporcional | `0.3` |
| **Kd** | Ganho derivativo | `0.7` |
| **Power** | Velocidade base do motor (−100 a 100) | `50` |

### Passo 2 — Bloco de controle (executar em cada iteração do laço)

| Parâmetro | Descrição |
|-----------|-----------|
| **P1** | Valor bruto do sensor de cor esquerdo |
| **P2** | Valor bruto do sensor de cor direito |

---

## Modos

| Modo | Categoria | Descrição |
|------|-----------|-----------|
| `Configuration` | — | Definir Kp, Kd, Power, Ports (chamar uma vez) |
| `P_Large` | P\_byVars | Controle P, portas de motor grande |
| `P_Medium` | P\_byVars | Controle P, portas de motor médio |
| `P_twoRev` | P\_byVars | Controle P, direção do motor invertida |
| `PD_Large` | PD\_byVars | Controle PD, portas de motor grande |
| `PD_Medium` | PD\_byVars | Controle PD, portas de motor médio |
| `PD_twoRev` | PD\_byVars | Controle PD, direção do motor invertida |
| `PDpwr_Large` | PDpwr\_byVars | PD + potência configurável, motores grandes |
| `PDpwr_Medium` | PDpwr\_byVars | PD + potência configurável, motores médios |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + potência configurável, invertido |

---

## Estrutura típica do laço

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## Dicas

- Comece com `Kp=0.3, Kd=0.7`. Aumente `Kp` para correção mais rápida; aumente `Kd` para reduzir oscilações.
- Use os modos `PDpwr_*` quando quiser variar a velocidade dinamicamente (por ex., combinado com o Acceleration Controller).
- Use `P_twoRev` / `PD_twoRev` se os motores esquerdo e direito do seu robô estiverem montados em orientações opostas.
