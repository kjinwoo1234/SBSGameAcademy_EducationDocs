# 애니메이션 블루프린트 기초

캐릭터 애니메이션은 Animation Blueprint의 상태 머신과 BlendSpace를 함께 사용해 자연스럽게 전환합니다.

## 학습 목표
- Animation Blueprint의 기본 구조를 설명할 수 있다.
- Idle/Run/Jump 상태 전이를 설정할 수 있다.
- 속도 변수 기반 전환 로직을 구현할 수 있다.

## 핵심
- `Animation Blueprint`: 애니메이션 실행 로직 관리
- `State Machine`: 상태별 애니메이션 전이
- `BlendSpace`: 속도/방향 값에 따른 애니메이션 보간

## 단계별 실습
1. 캐릭터용 Animation Blueprint를 생성한다.
2. 상태 머신에 `Idle`, `Run`, `Jump` 상태를 추가한다.
3. `Speed` 변수를 갱신해 Idle/Run 전이 조건을 만든다.
4. Jump 관련 조건을 설정하고 전이 결과를 Play 모드에서 확인한다.

## 실수 포인트
- `Speed` 변수를 갱신하지 않아 상태 전이가 일어나지 않는다.
- 전이 조건이 서로 충돌해 특정 상태에 고정된다.

## 이해 점검 체크리스트
- 상태 머신에 기본 3개 상태를 구성했는가?
- 속도 변수로 전이 조건을 만들었는가?
- 이동/점프 상황에서 애니메이션이 자연스럽게 전환되는가?

## 다음 학습 추천
- [데이터 관리 (DataTable, SaveGame)](./08-data-management.md)

## 셀프 퀴즈
1. 상태 기반 애니메이션 전환을 구성하는 기능은 무엇인가?
2. 이동 속도에 따라 애니메이션을 부드럽게 바꾸는 기능은 무엇인가?

## 정답
1. State Machine
2. BlendSpace

---

[상위 문서로 돌아가기](./README.md)
