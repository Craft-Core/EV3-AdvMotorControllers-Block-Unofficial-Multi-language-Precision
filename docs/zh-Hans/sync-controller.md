# OFDL MoveSync Controller — 使用指南

在直线行驶过程中，通过P控制器持续修正编码器漂移，保持两个电机以相同速度运行。

---

## 概念

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

没有同步的情况下，即使是相同的电机在相同的指令速度下，也会因机械差异随时间产生偏差。该块消除了这种漂移。

---

## 设置

### 第一步 — 配置块（在循环前运行一次）

| 参数 | 说明 | 典型值 |
|------|------|--------|
| **Ports** | 电机端口 | `1.B+C` |
| **Kp** | 同步修正增益 | `0.12` |
| **Power1** | 左电机基础速度（−100 到 100） | `50` |
| **Power2** | 右电机基础速度（−100 到 100） | `50` |
| **LeftInversed** | 反转左电机方向 | `False` |
| **RightInversed** | 反转右电机方向 | `False` |

### 第二步 — 同步块（每次循环迭代时运行）

| 参数 | 说明 |
|------|------|
| **E1** | 左侧编码器读数 |
| **E2** | 右侧编码器读数 |

---

## 模式

| 模式 | 说明 |
|------|------|
| `Configuration` | 设置 Kp、Power1、Power2、Ports、反转标志（调用一次） |
| `SYNC_Std` | 使用已配置的 Power1/Power2 进行同步 |
| `SYNC_byPwr` | 使用每次迭代传入的 Power1/Power2 进行同步（覆盖配置） |

---

## 典型循环结构

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

或使用动态功率：

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## 提示

- 设置 `Power1 = Power2` 以直线行驶。有意设置不等值会产生稳定的弧线。
- 如果电机物理上呈镜像安装（一个朝前，一个朝后），请使用 `LeftInversed` / `RightInversed`。
- 增大 `Kp` 可实现更精确的同步；如果机器人出现振荡，请减小该值。
- 结合**加速控制器**，在加速和减速过程中保持电机同步：将 `Output` 输入到 `SYNC_byPwr`。
