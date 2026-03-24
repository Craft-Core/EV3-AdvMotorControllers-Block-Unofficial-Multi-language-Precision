# OFDL MoveSync Controller — Guia de uso

Mantém dois motores na mesma velocidade durante o deslocamento em linha reta corrigindo continuamente a deriva do encoder usando um controlador P.

---

## Conceito

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Sem sincronização, mesmo motores idênticos na mesma velocidade comandada irão derivar com o tempo devido a diferenças mecânicas. Este bloco elimina essa deriva.

---

## Configuração

### Passo 1 — Bloco de configuração (executar uma vez antes do laço)

| Parâmetro | Descrição | Valor típico |
|-----------|-----------|-------------|
| **Ports** | Portas dos motores | `1.B+C` |
| **Kp** | Ganho de correção de sincronização | `0.12` |
| **Power1** | Velocidade base do motor esquerdo (−100 a 100) | `50` |
| **Power2** | Velocidade base do motor direito (−100 a 100) | `50` |
| **LeftInversed** | Inverter direção do motor esquerdo | `False` |
| **RightInversed** | Inverter direção do motor direito | `False` |

### Passo 2 — Bloco de sincronização (executar em cada iteração do laço)

| Parâmetro | Descrição |
|-----------|-----------|
| **E1** | Leitura do encoder esquerdo |
| **E2** | Leitura do encoder direito |

---

## Modos

| Modo | Descrição |
|------|-----------|
| `Configuration` | Definir Kp, Power1, Power2, Ports, sinalizadores de inversão (chamar uma vez) |
| `SYNC_Std` | Sincronização com Power1/Power2 configurados |
| `SYNC_byPwr` | Sincronização com Power1/Power2 passados em cada iteração (substitui a config) |

---

## Estrutura típica do laço

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

Com potência dinâmica:

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## Dicas

- Defina `Power1 = Power2` para deslocamento reto. Valores intencionalmente desiguais produzirão um arco consistente.
- Use `LeftInversed` / `RightInversed` se seus motores estiverem fisicamente espelhados (um voltado para frente, o outro para trás).
- Aumente `Kp` para sincronização mais precisa; se o robô oscilar, reduza-o.
- Combine com o **Acceleration Controller** para manter os motores sincronizados durante a rampa de aceleração e desaceleração: alimente `Output` em `SYNC_byPwr`.
