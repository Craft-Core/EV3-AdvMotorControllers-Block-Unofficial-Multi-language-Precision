# OFDL MoveSync Controller — 使い方ガイド

Pコントローラーを使用してエンコーダードリフトを継続的に補正することで、直進走行中に2つのモーターを同じ速度で維持します。

---

## 概念

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

同期なしでは、同一のモーターでも同じ指令速度で時間とともに機械的な差異によりずれが生じます。このブロックはそのドリフトを解消します。

---

## セットアップ

### ステップ1 — 設定ブロック（ループ前に1回実行）

| パラメーター | 説明 | 典型的な値 |
|------------|------|----------|
| **Ports** | モーターポート | `1.B+C` |
| **Kp** | 同期補正ゲイン | `0.12` |
| **Power1** | 左モーターのベース速度（−100〜100） | `50` |
| **Power2** | 右モーターのベース速度（−100〜100） | `50` |
| **LeftInversed** | 左モーターの方向を反転 | `False` |
| **RightInversed** | 右モーターの方向を反転 | `False` |

### ステップ2 — 同期ブロック（ループの各反復で実行）

| パラメーター | 説明 |
|------------|------|
| **E1** | 左エンコーダーの読み取り値 |
| **E2** | 右エンコーダーの読み取り値 |

---

## モード

| モード | 説明 |
|-------|------|
| `Configuration` | Kp、Power1、Power2、Ports、反転フラグを設定（1回呼び出す） |
| `SYNC_Std` | 設定されたPower1/Power2を使用して同期 |
| `SYNC_byPwr` | 各反復でPower1/Power2を渡して同期（設定を上書き） |

---

## 典型的なループ構造

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

動的なパワーを使用する場合:

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## ヒント

- 直進走行には `Power1 = Power2` に設定してください。意図的に不均等な値を設定すると、一定の弧を描きます。
- モーターが物理的に鏡像配置（一方が前向き、他方が後向き）の場合は `LeftInversed` / `RightInversed` を使用してください。
- 同期を厳密にするには `Kp` を増やし、ロボットが振動する場合は減らしてください。
- 加速・減速中にモーターの同期を維持するために **Acceleration Controller** と組み合わせてください。`Output` を `SYNC_byPwr` に入力します。
