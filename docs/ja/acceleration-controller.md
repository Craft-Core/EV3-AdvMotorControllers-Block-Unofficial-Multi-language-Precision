# OFDL Acceleration Controller — 使い方ガイド

ライブエンコーダーの読み取りに基づいて、`AccelDist` 度にわたって `MinPwr` から `MaxPwr` へ**加速**し、中間を `MaxPwr` で走行し、その後 `DecelDist` 度にわたって `MinPwr` へ**減速**する、滑らかな速度プロファイルを生成します。

---

## 概念

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

このブロックは**速度値を出力します**。ループの各反復でモーターのパワー入力に接続してください。モーターを直接駆動するわけではありません。

---

## セットアップ

### ステップ1 — 設定ブロック（ループ前に1回実行）

| パラメーター | 説明 | 典型的な値 |
|------------|------|----------|
| **EncPort** | エンコーダーモーターポート | `1.B+C` |
| **MaxPwr** | 最大速度（−100〜100） | `90` |
| **MinPwr** | 開始/終了時の最小速度（−100〜100） | `15` |
| **AccelDist** | 加速距離（度） | `300` |
| **DecelDist** | 減速距離（度） | `300` |
| **FullDist** | 総走行距離（度） | `1000` |

### ステップ2 — 出力ブロック（ループの各反復で実行）

#### 出力

| 出力 | 説明 |
|-----|------|
| **Output** | モーターに適用する現在の速度 |
| **E1Output** | エンコーダー1の現在の読み取り値（度） |
| **E2Output** | エンコーダー2の現在の読み取り値（度） |
| **Finish** | `FullDist` に達したとき `True` |

---

## モード

| モード | 説明 |
|-------|------|
| `Configuration` | すべてのパラメーターを設定（ループ前に1回呼び出す） |
| `ACDC_Output1Degs` | エンコーダー1のみを使用して速度を出力 |
| `ACDC_Output2Degs` | 両方のエンコーダーを使用して速度を出力（平均値） |

---

## 典型的なループ構造

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## ヒント

- 開始前に必ずエンコーダーを**リセット**してください（Advanced Encoder の `EncReset` ブロックを使用）。
- `AccelDist + DecelDist` は `FullDist` より小さくなければなりません。そうでないとプロファイルが重なります。
- 可変速度での滑らかなライントレースには **PD Controller**（`PDpwr_*` モード）と組み合わせてください。`Output` をPDブロックのパワー入力として使用します。
- 単一モーターのタスクには `ACDC_Output1Degs` を使用し、エンコーダードリフトを考慮するために2モーター駆動には `ACDC_Output2Degs` を使用してください。
