# OFDL Acceleration Controller — 사용 가이드

실시간 엔코더 읽기를 기반으로 `AccelDist` 도(degree) 동안 `MinPwr`에서 `MaxPwr`로 **가속**, 중간 구간은 `MaxPwr`로 유지, 그런 다음 `DecelDist` 도 동안 `MinPwr`로 **감속**하는 부드러운 속도 프로파일을 생성합니다.

---

## 개념

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

블록은 **속도 값을 출력**합니다 — 매 루프 반복마다 이것을 모터 출력 입력에 연결하세요. 모터를 직접 구동하지는 않습니다.

---

## 설정

### 1단계 — 구성 블록 (루프 전에 한 번 실행)

| 파라미터 | 설명 | 일반적인 값 |
|----------|------|-------------|
| **EncPort** | 엔코더 모터 포트 | `1.B+C` |
| **MaxPwr** | 최고 속도 (−100 ~ 100) | `90` |
| **MinPwr** | 시작/끝 최소 속도 (−100 ~ 100) | `15` |
| **AccelDist** | 가속 거리 (도) | `300` |
| **DecelDist** | 감속 거리 (도) | `300` |
| **FullDist** | 총 이동 거리 (도) | `1000` |

### 2단계 — 출력 블록 (매 루프 반복마다 실행)

#### 출력

| 출력 | 설명 |
|------|------|
| **Output** | 모터에 적용할 현재 속도 |
| **E1Output** | 현재 엔코더 1 값 (도) |
| **E2Output** | 현재 엔코더 2 값 (도) |
| **Finish** | `FullDist`에 도달했을 때 `True` |

---

## 모드

| 모드 | 설명 |
|------|------|
| `Configuration` | 모든 파라미터 설정 (루프 전에 한 번 호출) |
| `ACDC_Output1Degs` | 엔코더 1만 사용하여 속도 출력 |
| `ACDC_Output2Degs` | 두 엔코더를 사용하여 속도 출력 (평균) |

---

## 일반적인 루프 구조

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## 팁

- 시작 전에 항상 **엔코더를 초기화**하세요 (Advanced Encoder `EncReset` 블록 사용).
- `AccelDist + DecelDist`는 `FullDist`보다 작아야 합니다. 그렇지 않으면 프로파일이 겹칩니다.
- 가변 속도로 부드러운 라인 추적을 위해 **PD 컨트롤러** (`PDpwr_*` 모드)와 결합하세요: `Output`을 PD 블록의 출력 입력으로 연결하세요.
- 단일 모터 작업에는 `ACDC_Output1Degs`를 사용하고, 엔코더 드리프트를 보정하려면 2모터 드라이브에 `ACDC_Output2Degs`를 사용하세요.
