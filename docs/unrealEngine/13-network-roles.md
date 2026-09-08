# Chapter 13 네트워크 역할·권한

## 학습 목표

- Authority, Owner, Role의 의미를 구분할 수 있다.
- GameMode · GameState · PlayerState의 네트워크 책임을 나눌 수 있다.
- 잘못 둔 로직을 역할 기준으로 찾아 옮길 수 있다.

## 본문

### 13-1 Authority · Owner · Role

| 용어 | 의미 |
|---|---|
| **Authority** | 이 액터의 권한 있는 시뮬레이션이 여기 있는가. 보통 서버 |
| **Owner** | 이 액터를 소유한 연결. 무기·폰과 입력에 중요 |
| **Role** | `ROLE_Authority`, `ROLE_AutonomousProxy`, `ROLE_SimulatedProxy` 등 |

로컬 플레이어의 폰은 Autonomous Proxy로 입력을 바로 반영하고, 다른 플레이어 폰은 Simulated Proxy로 복제된 이동을 보는 식입니다.

`HasAuthority()`가 참일 때만 체력·점수를 바꾸도록 가드하는 습관이 필요합니다. Server RPC와 오너십의 기본 관계는 Chapter 09에서 이미 다뤘습니다. 이 장은 용어·Role·GameState까지 표로 고정합니다.

### 13-2 GameMode · GameState · PlayerState

| 클래스 | 복제 | 역할 |
|---|---|---|
| **GameMode** | 서버만 | 규칙, 스폰, 승패. 클라이언트에 없음 |
| **GameState** | 복제됨 | 모두가 알아야 하는 경기 상태. 남은 시간 등 |
| **PlayerState** | 복제됨 | 플레이어별 점수·이름 |

클라이언트가 GameMode 포인터를 직접 쓰려다 null이 되는 실수를 자주 합니다. 클라이언트에서 읽을 규칙은 GameState로 내려보냅니다.

### 13-3 책임 분리 실습 관점

- 점수 가산: 서버, PlayerState
- 남은 시간 UI: GameState의 복제 시간
- 스폰 위치 선택: GameMode
- 내 체력바: 내 폰/컴포넌트 복제 값

역할을 섞으면 “내 PC에서만 동작” 버그가 납니다. PIE 2인에서 호스트와 클라이언트 창을 번갈아 보며 어느 Role인지 확인합니다.

이동 동기화 세부는 Chapter 14에서 다룹니다.

### 13-4 따라하기 — 역할별 책임 분리

1. 점수 가산을 PlayerState 서버 경로로만 남긴다.
2. 남은 시간 또는 라운드 상태를 GameState 복제 변수로 둔다.
3. 스폰·승패는 GameMode에만 둔다.
4. 클라이언트에서 GameMode를 직접 쓰지 않는지 검색해 제거한다.
5. PIE 2인에서 점수·시간이 양쪽에서 맞는지 확인한다.

### 연습문제

1. 문제: 클라이언트에서 GameMode를 직접 쓰면 안 되는 이유를 한 문장으로 쓰세요.  
2. 문제: 점수와 남은 시간 중 GameState에 두기 좋은 것을 고르고 이유를 쓰세요.  
3. 문제: Autonomous Proxy와 Simulated Proxy의 차이를 두 문장으로 쓰세요.

### 정답 포인트

- GameMode는 서버 전용. 남은 시간은 전원 공유 → GameState. Autonomous=내 입력, Simulated=남 복제.

---

[상위 문서로 돌아가기](./README.md)
