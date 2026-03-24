# OFDL PD Controller — 使用指南

一种巡线PD（比例-微分）控制器，读取两个颜色传感器的值并驱动两个电机，使机器人保持在线的中心位置。

---

## 概念

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **仅P控制**（`P_*` 模式）：仅使用 `Kp × error`
- **PD控制**（`PD_*` 模式）：同时使用 `Kp` 和 `Kd`
- **PDpwr**（`PDpwr_*` 模式）：与PD相同，但从全局配置读取Power

---

## 设置

### 第一步 — 配置块（在循环前运行一次）

| 参数 | 说明 | 典型值 |
|------|------|--------|
| **Ports** | 电机端口 (B+C) | `1.B+C` |
| **Kp** | 比例增益 | `0.3` |
| **Kd** | 微分增益 | `0.7` |
| **Power** | 基础电机速度（−100 到 100） | `50` |

### 第二步 — 控制块（每次循环迭代时运行）

| 参数 | 说明 |
|------|------|
| **P1** | 左侧颜色传感器原始值 |
| **P2** | 右侧颜色传感器原始值 |

---

## 模式

| 模式 | 类别 | 说明 |
|------|------|------|
| `Configuration` | — | 设置 Kp、Kd、Power、Ports（调用一次） |
| `P_Large` | P\_byVars | P控制，大型电机端口 |
| `P_Medium` | P\_byVars | P控制，中型电机端口 |
| `P_twoRev` | P\_byVars | P控制，电机方向反转 |
| `PD_Large` | PD\_byVars | PD控制，大型电机端口 |
| `PD_Medium` | PD\_byVars | PD控制，中型电机端口 |
| `PD_twoRev` | PD\_byVars | PD控制，电机方向反转 |
| `PDpwr_Large` | PDpwr\_byVars | PD + 可配置功率，大型电机 |
| `PDpwr_Medium` | PDpwr\_byVars | PD + 可配置功率，中型电机 |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + 可配置功率，方向反转 |

---

## 典型循环结构

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## 提示

- 从 `Kp=0.3, Kd=0.7` 开始。增大 `Kp` 可加快修正速度；增大 `Kd` 可减少振荡。
- 当需要动态改变速度时（例如结合加速控制器使用），请使用 `PDpwr_*` 模式。
- 如果机器人的左右电机安装方向相反，请使用 `P_twoRev` / `PD_twoRev`。
