# OFDL MoveSync Controller — 사용 가이드

P 컨트롤러를 사용하여 엔코더 드리프트를 지속적으로 수정함으로써 직선 주행 중 두 모터가 동일한 속도로 작동하도록 유지합니다.

---

## 개념

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

동기화 없이는 동일한 명령 속도에서도 동일한 모터라도 기계적 차이로 인해 시간이 지남에 따라 차이가 발생합니다. 이 블록이 그 드리프트를 제거합니다.

---

## 설정

### 1단계 — 구성 블록 (루프 전에 한 번 실행)

| 파라미터 | 설명 | 일반적인 값 |
|----------|------|-------------|
| **Ports** | 모터 포트 | `1.B+C` |
| **Kp** | 동기화 수정 게인 | `0.12` |
| **Power1** | 왼쪽 모터 기본 속도 (−100 ~ 100) | `50` |
| **Power2** | 오른쪽 모터 기본 속도 (−100 ~ 100) | `50` |
| **LeftInversed** | 왼쪽 모터 방향 반전 | `False` |
| **RightInversed** | 오른쪽 모터 방향 반전 | `False` |

### 2단계 — 동기화 블록 (매 루프 반복마다 실행)

| 파라미터 | 설명 |
|----------|------|
| **E1** | 왼쪽 엔코더 값 |
| **E2** | 오른쪽 엔코더 값 |

---

## 모드

| 모드 | 설명 |
|------|------|
| `Configuration` | Kp, Power1, Power2, Ports, 반전 플래그 설정 (한 번 호출) |
| `SYNC_Std` | 설정된 Power1/Power2를 사용한 동기화 |
| `SYNC_byPwr` | 각 반복마다 전달되는 Power1/Power2로 동기화 (설정 덮어쓰기) |

---

## 일반적인 루프 구조

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

또는 동적 출력을 사용하는 경우:

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## 팁

- 직선 주행을 위해 `Power1 = Power2`로 설정하세요. 의도적으로 불균등한 값은 일정한 호를 만들어냅니다.
- 모터가 물리적으로 미러링된 경우 (한쪽은 앞을 향하고 다른 쪽은 뒤를 향함) `LeftInversed` / `RightInversed`를 사용하세요.
- 더 정밀한 동기화를 위해 `Kp`를 높이세요. 로봇이 진동하면 낮추세요.
- 가속 및 감속 중에도 모터를 동기화 상태로 유지하려면 **가속 컨트롤러**와 결합하세요: `Output`을 `SYNC_byPwr`에 연결하세요.
