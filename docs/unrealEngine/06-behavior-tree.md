# Chapter 06 비헤이비어 트리

## 학습 목표

- 블랙보드와 비헤이비어 트리의 역할을 구분할 수 있다.
- 추격·순찰 태스크를 트리 레이아웃으로 구성할 수 있다.
- AI 컨트롤러와 데코레이터의 관계를 설명할 수 있다.
- Behavior Tree 디버깅으로 실패 지점을 좁힐 수 있다.

## 본문

### 06-1 블랙보드와 트리

**블랙보드**는 AI가 공유하는 키-값 저장소입니다. “현재 목표 액터”, “순찰 지점”, “시야에 있음” 같은 값을 둡니다.

**비헤이비어 트리**는 그 값을 읽어 **어떤 행동을 어떤 순서로** 할지 정하는 그래프입니다. 루트 아래 Selector·Sequence·Task·Decorator를 배치합니다.

| 노드 | 역할 |
|---|---|
| Selector | 자식 중 성공하는 쪽을 고름 |
| Sequence | 자식을 앞에서부터 순서대로, 실패 시 중단 |
| Task | 실제 행동. 이동, 대기, 공격 |
| Decorator | 조건. 성공·실패를 가로채 실행 여부 결정 |

![Behavior Tree 에디터: Root 아래 Selector·Sequence·Task·Decorator가 배치된 레이아웃 예시](./img/06-behavior-tree-layout.png)

### 06-2 AI 컨트롤러

캐릭터에 바로 트리를 돌리기보다 **AI Controller**가 폰을 Possess하고 트리를 실행하는 구조가 흔합니다.

1. `AIController` 블루프린트 또는 C++ 클래스를 만든다.
2. Behavior Tree·Blackboard 에셋을 지정한다.
3. 적 캐릭터의 AI Controller Class를 그것으로 설정한다.
4. `BeginPlay` 또는 Possess 시 `RunBehaviorTree`를 호출한다.

플레이어가 조종하는 캐릭터는 Player Controller, 적은 AI Controller입니다. 같은 `ACharacter`를 쓰더라도 컨트롤러가 다릅니다.

### 06-3 추격과 순찰

초보용 트리는 단순하게 갑니다.

1. Decorator: 블랙보드에 Enemy 키가 유효한가
2. 유효하면 Task: Move To Enemy
3. 아니면 Sequence: 순찰 지점으로 Move To → Wait

랜덤 순찰은 순찰 지점 배열에서 인덱스를 고르거나, EQS 없이 레벨에 둔 Target Point를 순서대로 방문해도 됩니다. EQS는 환경에서 위치를 고르는 심화 도구이므로 이 과정 필수 범위 밖입니다.

### 06-4 BT 디버깅

**BT 디버깅**은 “트리가 지금 어느 노드를 실행 중인가, 왜 다른 가지로 갔는가”를 보는 일입니다.

1. PIE를 실행하고 해당 AI 폰 또는 AI Controller를 선택한다.
2. Behavior Tree 에디터 창을 연다. 실행 중 **활성 노드**가 강조된다.

![PIE 중 Behavior Tree 창에서 활성 Task가 강조되고 Blackboard 키 값이 보이는 디버그 화면](./img/06-bt-debug-active-node.png)

3. 기대한 Task로 들어가지 않으면 Decorator 조건부터 본다. Blackboard 키가 None인지, bool이 false인지 확인한다.
4. Blackboard 창에서 키 값이 기대한 액터·벡터로 바뀌는지 본다.
5. Move To가 실패하면 트리 논리보다 NavMesh·Possess 문제일 수 있다. Chapter 07로 이어진다.
6. 로그에 `RunBehaviorTree` 실패, Missing Blackboard Asset이 있으면 에셋 연결부터 고친다.

자주 하는 실수: Enemy 키를 시야 코드에서 한 번도 세팅하지 않음, Decorator Abort 설정 때문에 순찰이 즉시 끊김, AI Controller Class 미지정.

### 06-5 따라하기 — AI 적 캐릭터

1. 적 캐릭터와 AI Controller를 만든다.
2. Blackboard에 Enemy, PatrolPoint 키를 둔다.
3. Behavior Tree로 추격/순찰 Selector를 구성한다.
4. 플레이어가 보이면 Enemy를 세팅하는 감지 로직을 최소로 넣는다.
5. PIE에서 BT 디버깅으로 추격·순찰 전환을 확인한다.

이동이 “제자리”이거나 벽을 못 넘으면 NavMesh 문제인 경우가 많습니다. 메시 빌드와 링크는 Chapter 07에서 다룹니다.

### 연습문제

1. 문제: 블랙보드에 두기 좋은 키 예를 두 개 드세요.  
2. 문제: Selector와 Sequence의 차이를 두 문장으로 쓰세요.  
3. 문제: 추격이 안 될 때 BT 디버깅에서 먼저 볼 것 두 가지를 쓰세요.

### 정답 포인트

- Enemy, PatrolPoint 등. Selector=택일, Sequence=순차. 활성 노드·블랙보드 키 값.

---

[상위 문서로 돌아가기](./README.md)
