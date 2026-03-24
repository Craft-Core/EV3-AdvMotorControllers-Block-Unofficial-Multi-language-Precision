# OFDL Acceleration Controller — 使用指南

根据实时编码器读数，生成平滑的速度曲线：在 `AccelDist` 度内从 `MinPwr` **加速**到 `MaxPwr`，在中间段以 `MaxPwr` 运行，然后在 `DecelDist` 度内**减速**回到 `MinPwr`。

---

## 概念

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

该块**输出一个速度值** — 每次循环迭代时将其连接到电机功率输入。它不直接驱动电机。

---

## 设置

### 第一步 — 配置块（在循环前运行一次）

| 参数 | 说明 | 典型值 |
|------|------|--------|
| **EncPort** | 编码器电机端口 | `1.B+C` |
| **MaxPwr** | 峰值速度（−100 到 100） | `90` |
| **MinPwr** | 起始/结束最小速度（−100 到 100） | `15` |
| **AccelDist** | 加速距离（度） | `300` |
| **DecelDist** | 减速距离（度） | `300` |
| **FullDist** | 总行驶距离（度） | `1000` |

### 第二步 — 输出块（每次循环迭代时运行）

#### 输出

| 输出 | 说明 |
|------|------|
| **Output** | 当前应用于电机的速度 |
| **E1Output** | 当前编码器1读数（度） |
| **E2Output** | 当前编码器2读数（度） |
| **Finish** | 达到 `FullDist` 时为 `True` |

---

## 模式

| 模式 | 说明 |
|------|------|
| `Configuration` | 设置所有参数（在循环前调用一次） |
| `ACDC_Output1Degs` | 仅使用编码器1输出速度 |
| `ACDC_Output2Degs` | 使用两个编码器输出速度（取平均值） |

---

## 典型循环结构

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## 提示

- 开始前务必**重置编码器**（使用高级编码器 `EncReset` 块）。
- `AccelDist + DecelDist` 必须小于 `FullDist`，否则速度曲线会重叠。
- 结合 **PD控制器**（`PDpwr_*` 模式）实现变速平稳巡线：将 `Output` 作为功率输入连接到PD块。
- 单电机任务使用 `ACDC_Output1Degs`；双电机驱动使用 `ACDC_Output2Degs` 以补偿编码器漂移。
