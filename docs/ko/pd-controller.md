# OFDL PD Controller — 사용 가이드

두 개의 컬러 센서를 읽어 두 개의 모터를 구동하여 로봇이 라인 중앙을 유지하도록 하는 라인 추적 PD(비례-미분) 컨트롤러입니다.

---

## 개념

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **P 제어만 사용** (`P_*` 모드): `Kp × error`만 사용
- **PD 제어** (`PD_*` 모드): `Kp`와 `Kd` 모두 사용
- **PDpwr** (`PDpwr_*` 모드): PD와 동일하지만 전역 설정에서 Power를 읽음

---

## 설정

### 1단계 — 구성 블록 (루프 전에 한 번 실행)

| 파라미터 | 설명 | 일반적인 값 |
|----------|------|-------------|
| **Ports** | 모터 포트 (B+C) | `1.B+C` |
| **Kp** | 비례 게인 | `0.3` |
| **Kd** | 미분 게인 | `0.7` |
| **Power** | 기본 모터 속도 (−100 ~ 100) | `50` |

### 2단계 — 제어 블록 (매 루프 반복마다 실행)

| 파라미터 | 설명 |
|----------|------|
| **P1** | 왼쪽 컬러 센서 원시 값 |
| **P2** | 오른쪽 컬러 센서 원시 값 |

---

## 모드

| 모드 | 카테고리 | 설명 |
|------|----------|------|
| `Configuration` | — | Kp, Kd, Power, Ports 설정 (한 번 호출) |
| `P_Large` | P\_byVars | P 제어, 대형 모터 포트 |
| `P_Medium` | P\_byVars | P 제어, 중형 모터 포트 |
| `P_twoRev` | P\_byVars | P 제어, 모터 방향 반전 |
| `PD_Large` | PD\_byVars | PD 제어, 대형 모터 포트 |
| `PD_Medium` | PD\_byVars | PD 제어, 중형 모터 포트 |
| `PD_twoRev` | PD\_byVars | PD 제어, 모터 방향 반전 |
| `PDpwr_Large` | PDpwr\_byVars | PD + 조정 가능한 출력, 대형 모터 |
| `PDpwr_Medium` | PDpwr\_byVars | PD + 조정 가능한 출력, 중형 모터 |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + 조정 가능한 출력, 방향 반전 |

---

## 일반적인 루프 구조

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## 팁

- `Kp=0.3, Kd=0.7`로 시작하세요. 빠른 수정을 위해 `Kp`를 높이고, 진동을 줄이려면 `Kd`를 높이세요.
- 속도를 동적으로 변경하려면 `PDpwr_*` 모드를 사용하세요 (예: 가속 컨트롤러와 함께 사용).
- 로봇의 왼쪽과 오른쪽 모터가 반대 방향으로 장착된 경우 `P_twoRev` / `PD_twoRev`를 사용하세요.
