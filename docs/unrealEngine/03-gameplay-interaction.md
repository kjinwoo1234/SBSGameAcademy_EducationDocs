# Chapter 03 게임 상호작용 기능 추가

## 학습 목표
- 입력 매핑과 향상된 입력(Enhanced Input)의 흐름을 구분한다.
- 물리 시뮬레이션, 오버랩, 라인 트레이스, Physics Handle을 목적에 맞게 선택한다.

## 세부 주제
- 액터 움직이기
- 키보드·마우스 입력 — 간단한 입력 이벤트, 축 매핑, **Enhanced Input**
- (실습) 장애물 피하기 — Overlap으로 도착 지점
- 피직스 시뮬레이션, 투사체 스폰
- (실습) 창고 부수기 — 물체 합치기·파괴 연출
- (실습) 탐험 게임 — Enhanced Input, Line Trace, 컴포넌트, Physics Handle, 기믹, 씬 이동

## 실습 체크리스트
- 프로젝트 설정에서 Axis/Action 또는 Enhanced Input 액션을 등록하고 Pawn에서 호출한다.
- `Simulate Physics`가 켜진 메시에 힘(Impulse)을 가해 본다.
- `LineTraceSingleByChannel`으로 바닥 또는 오브젝트를 검출한다.

## 본문

### 3-1 액터 움직이기

- **Tick** 또는 **입력 이벤트**에서 `Add Actor World Offset`, `Set Actor Location` 등으로 위치를 바꿉니다.
- **Character**는 `CharacterMovementComponent`가 내장되어 있어 지면 이동·점프에 적합합니다.

---

### 3-2 키보드·마우스 입력으로 캐릭터 조종하기

#### 간단한 입력 이벤트

- **Project Settings → Input**에서 **Action/Axis Mappings**을 추가합니다.
- Blueprint에서 **InputAction / Axis** 이벤트 노드로 키 입력에 반응합니다.

#### 축 매핑 후 코드 실행

- **Axis Value**는 보통 -1~1 범위입니다. `Add Movement Input` 등에 연결합니다.
- 같은 축에 WASD와 게임패드 스틱을 함께 매핑할 수 있습니다.

학습 목표 연결 — 구 방식 입력 vs **Enhanced Input**(향상된 입력):

| 구분 | **Project Settings → Input**(Action/Axis) | **Enhanced Input** |
|------|-------------------------------------------|---------------------|
| 설정 위치 | 프로젝트 전역 매핑 테이블 | **Input Action**·**Mapping Context** 자산 |
| 장점 | 설정이 단순해 보이고 빠르게 테스트하기 쉬움 | 입력을 모듈로 나누어 **리매핑·로컬 멀티·난이도별 프리셋**에 유리한 경우가 많음 |
| 단점 | 프로젝트가 커지면 매핑이 한곳에 몰리기 쉬움 | 처음에 자산 종류가 많아 보일 수 있음 |
| 입문자 기준 | 작은 예제·레거튜토리얼과 맞추기 좋음 | 3-7 탐험 실습·신규 템플릿 흐름과 맞추기 좋음 |

---

### 3-3 (실습) 장애물 피하기 — Overlap으로 도착 지점

- **Collision**에서 **Overlap** 이벤트를 쓰려면 대상 프리셋이 서로 오버랩에 응답하도록 설정해야 합니다.
- **On Actor Begin Overlap**에서 도착 플래그를 세우거나 UI를 띄웁니다.

오류 방지:
1. **언제**: 한쪽만 Block, 다른 쪽은 Ignore일 때 이벤트가 안 뜰 수 있음.
2. **왜**: 채널·프리셋이 물리 응답과 이벤트 응답을 동시에 제어함.
3. **해결**: **Collision Presets**와 **Object Type**을 맞추고, 필요 시 **Generate Overlap Events** 확인.

---

### 3-4 피직스 시뮬레이션으로 물리 효과 구현

- Static Mesh에 **Simulate Physics**를 켜면 중력·충돌에 의해 움직입니다.
- **Mass, Linear Damping, Friction**으로 느낌을 조절합니다.

---

### 3-5 투사체 스폰시켜 발사하기

- **Spawn Actor from Class**로 Projectile 블루프린트를 생성하고 **Initial Velocity**를 줍니다.
- **Projectile Movement Component**는 추적·중력 옵션을 단순화합니다.

---

### 3-6 (실습) 창고 부수기 — 물체 합치기

- **Constraint** 또는 **Welding**(같은 물리 바디로 묶기)으로 파편을 한 덩어리처럼 다룰 수 있습니다.
- **Geometry Collection**(Chaos)로 파괴 가능 메시를 만들면 창고 붕괴 연출에 적합합니다.

---

### 3-7 (실습) 탐험 게임

#### 향상된 입력 시스템

- **Enhanced Input**은 **Input Mapping Context**, **Input Action**, **Modifier/Trigger**로 입력을 모듈화합니다.
- 로컬 멀티·리매핑·난이도별 입력에 유리합니다.

오류 방지(Enhanced Input):
1. **언제**: Input Action을 만들었는데 키를 눌러도 이벤트가 한 번도 오지 않을 때.
2. **왜**: **Mapping Context**를 플레이어 **Enhanced Input** 컴포넌트에 **Add Mapping Context**로 등록하지 않았거나, 우선순위·활성화 시점이 맞지 않으면 입력이 전달되지 않습니다.
3. **해결**: 캐릭터 **Begin Play** 등에서 해당 Context를 추가했는지, **Default Mapping Context** 설정(프로젝트/캐릭터)과 중복·충돌이 없는지 확인합니다.

#### LineTrace로 물체 감지

- **Line Trace By Channel**으로 시선 방향 레이를 쏘고 **Hit Result**에서 액터·임팩트 노멀을 읽습니다.

#### 컴포넌트 시스템

- 기능은 **컴포넌트**로 나누면 재사용과 테스트가 쉬워집니다(예: 상호작용, 인벤토리).

#### PhysicsHandleComponent로 물체 조종

- 잡기·들기·던지기는 **Physics Handle**로 구현하기 좋습니다. 질량·채널 설정을 맞춥니다.

#### 기믹·씬 이동

- **Level Blueprint** 또는 **게임 모드**에서 트리거와 연동해 문 열기, **Open Level**로 씬 전환을 처리합니다.

용어 설명:

| 용어 | 의미 |
|------|------|
| Pawn | 플레이어 또는 AI가 **조종**할 수 있는 월드 안의 대표 액터(캐릭터의 기반이 되는 클래스) |

용어 설명(Enhanced Input):
- **Enhanced Input**(향상된 입력)은 언리얼 엔진의 **차세대 입력 플러그인**으로, 구 방식 **Project Settings → Input** 매핑과 병행하거나 대체해 쓸 수 있습니다.
- **Input Action**, **Mapping Context**, **Modifier**로 입력을 나누어 **리매핑·로컬 멀티·난이도별 프리셋**을 데이터 자산으로 관리하기 쉽습니다.
- 구 방식 대비 설정 단계가 많아 보일 수 있으나, 프로젝트가 커질수록 **입력 충돌을 줄이는 데** 유리한 경우가 많습니다.

예시: 블루프린트에서 Line Trace(의사 단계)
1. 이벤트(예: **Tick** 또는 **입력 액션**)에서 **Get Actor Location**과 **Get Forward Vector**로 시작점·방향을 구합니다.
2. **Line Trace By Channel** 노드에 **Start** = 카메라/캐릭터 눈 위치, **End** = Start + 방향 × 거리 를 연결합니다.
3. **Hit**이 true이면 **Hit Result**의 **Actor**를 분기해 상호작용 함수를 호출합니다.

예상 결과:
```text
Hit이 true일 때: Hit Actor가 유효하면 문 열기·아이템 줍기 등 분기 실행
Hit이 false일 때: 아무 것도 하지 않거나 빈 공간 클릭 처리
```

절차 해석:
- Line Trace는 **한 순간의 레이캐스트**이므로, 총알·시선 픽킹처럼 "직선상의 첫 충돌"을 알고 싶을 때 적합합니다.

연습문제:
1. 문제: Block과 Overlap의 차이를 "이벤트가 필요한 목적" 관점에서 한 문장씩 쓰세요.
   - 입력: 해당 없음
   - 출력: Block 한 문장, Overlap 한 문장
   - 조건: 물리적으로 막기 vs 감지하기 구분
2. 문제: Line Trace와 Overlap 중 "총알이 맞았는지" 판정에 더 흔한 방식은 무엇인지 이유와 함께 답하세요.
   - 입력: 해당 없음
   - 출력: 선택 + 이유 2문장 이상
   - 조건: 서버 권한 이야기가 있으면 가산
3. 문제: **Simulate Physics**를 켠 큐브에 **Add Impulse**를 한 번 주고, 질량을 바꿨을 때 날아가는 거리가 어떻게 달라지는지 기록하세요.
   - 입력: 동일 Impulse, 질량 10 vs 100
   - 출력: 관찰 결과 1~2문장
   - 조건: **Mass** 속성 위치(디테일 패널) 언급

정답 포인트:
- 입력 = 매핑 + 이벤트; 물리 = 시뮬레이션·투사체; 탐험 = Enhanced Input + Trace + Handle + 레벨 전환.

---

[상위 문서로 돌아가기](./README.md)
