# OFDL PD Controller — 使い方ガイド

2つのカラーセンサーを読み取り、2つのモーターを駆動してロボットをライン上に保持する、ライントレース用PD（比例微分）コントローラーです。

---

## 概念

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Pコントロールのみ**（`P_*` モード）: `Kp × error` のみを使用
- **PDコントロール**（`PD_*` モード）: `Kp` と `Kd` の両方を使用
- **PDpwr**（`PDpwr_*` モード）: PDと同じだが、Powerをグローバル設定から読み取る

---

## セットアップ

### ステップ1 — 設定ブロック（ループ前に1回実行）

| パラメーター | 説明 | 典型的な値 |
|------------|------|----------|
| **Ports** | モーターポート（B+C） | `1.B+C` |
| **Kp** | 比例ゲイン | `0.3` |
| **Kd** | 微分ゲイン | `0.7` |
| **Power** | ベースモーター速度（−100〜100） | `50` |

### ステップ2 — 制御ブロック（ループの各反復で実行）

| パラメーター | 説明 |
|------------|------|
| **P1** | 左カラーセンサーの生値 |
| **P2** | 右カラーセンサーの生値 |

---

## モード

| モード | カテゴリ | 説明 |
|-------|---------|------|
| `Configuration` | — | Kp、Kd、Power、Portsを設定（1回呼び出す） |
| `P_Large` | P\_byVars | Pコントロール、ラージモーターポート |
| `P_Medium` | P\_byVars | Pコントロール、ミディアムモーターポート |
| `P_twoRev` | P\_byVars | Pコントロール、逆方向モーター |
| `PD_Large` | PD\_byVars | PDコントロール、ラージモーターポート |
| `PD_Medium` | PD\_byVars | PDコントロール、ミディアムモーターポート |
| `PD_twoRev` | PD\_byVars | PDコントロール、逆方向モーター |
| `PDpwr_Large` | PDpwr\_byVars | PD＋設定可能なパワー、ラージモーター |
| `PDpwr_Medium` | PDpwr\_byVars | PD＋設定可能なパワー、ミディアムモーター |
| `PDpwr_twoRev` | PDpwr\_byVars | PD＋設定可能なパワー、逆方向 |

---

## 典型的なループ構造

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## ヒント

- まず `Kp=0.3, Kd=0.7` から始めてください。修正を速くするには `Kp` を増やし、振動を減らすには `Kd` を増やします。
- 速度を動的に変化させたい場合（例：加速コントローラーとの組み合わせ）は `PDpwr_*` モードを使用してください。
- ロボットの左右のモーターが逆向きに取り付けられている場合は `P_twoRev` / `PD_twoRev` を使用してください。
