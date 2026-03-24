# OFDL Acceleration Controller — Guia de uso

Gera um perfil de velocidade suave que **sobe** de `MinPwr` para `MaxPwr` ao longo de `AccelDist` graus, percorre o meio em `MaxPwr`, depois **desce** de volta a `MinPwr` ao longo de `DecelDist` graus — tudo baseado em leituras ao vivo dos encoders.

---

## Conceito

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

O bloco **emite um valor de velocidade** — conecte-o à entrada de potência do motor em cada iteração do laço. Ele não aciona os motores diretamente.

---

## Configuração

### Passo 1 — Bloco de configuração (executar uma vez antes do laço)

| Parâmetro | Descrição | Valor típico |
|-----------|-----------|-------------|
| **EncPort** | Portas dos motores encoders | `1.B+C` |
| **MaxPwr** | Velocidade máxima (−100 a 100) | `90` |
| **MinPwr** | Velocidade mínima no início/fim (−100 a 100) | `15` |
| **AccelDist** | Distância de aceleração (graus) | `300` |
| **DecelDist** | Distância de desaceleração (graus) | `300` |
| **FullDist** | Distância total de percurso (graus) | `1000` |

### Passo 2 — Bloco de saída (executar em cada iteração do laço)

#### Saídas

| Saída | Descrição |
|-------|-----------|
| **Output** | Velocidade atual a aplicar aos motores |
| **E1Output** | Leitura atual do encoder 1 (graus) |
| **E2Output** | Leitura atual do encoder 2 (graus) |
| **Finish** | `True` quando `FullDist` foi atingido |

---

## Modos

| Modo | Descrição |
|------|-----------|
| `Configuration` | Definir todos os parâmetros (chamar uma vez antes do laço) |
| `ACDC_Output1Degs` | Saída de velocidade usando apenas o encoder 1 |
| `ACDC_Output2Degs` | Saída de velocidade usando ambos os encoders (média) |

---

## Estrutura típica do laço

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## Dicas

- Sempre **redefina os encoders** antes de iniciar (use o bloco Advanced Encoder `EncReset`).
- `AccelDist + DecelDist` deve ser menor que `FullDist`, caso contrário os perfis se sobrepõem.
- Combine com o **PD Controller** (modos `PDpwr_*`) para seguimento de linha suave em velocidade variável: alimente `Output` como entrada de potência no bloco PD.
- Use `ACDC_Output1Degs` para tarefas de motor único; use `ACDC_Output2Degs` para acionamentos de dois motores para compensar a deriva do encoder.
