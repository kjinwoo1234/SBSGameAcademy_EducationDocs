# 입력 처리와 캐릭터 이동

입력 매핑을 먼저 정의하고 Character Blueprint에 연결하면 이동 시스템을 안정적으로 구성할 수 있습니다.

## 학습 목표
- 입력 매핑(Action/Axis)의 차이를 설명할 수 있다.
- Character Blueprint에서 이동 입력을 연결할 수 있다.
- Possess 설정 문제를 점검해 입력 누락을 해결할 수 있다.

## 핵심
- `Input Mapping`: 키/패드 입력을 액션으로 추상화
- `Character Blueprint`: 입력 이벤트를 이동 로직으로 연결
- `Add Movement Input`: 방향 벡터 기반 이동 처리

## 버전별 입력 시스템 안내
- **UE5 권장:** Enhanced Input 사용 (`Input Action`, `Input Mapping Context`)
- **레거시 방식:** Project Settings의 Action/Axis Mapping 사용
- 실습 전 프로젝트가 어떤 입력 시스템을 쓰는지 먼저 확인한다.
- 팀 프로젝트에서는 한 방식으로 통일해 혼선을 줄인다.

## 단계별 실습
1. 프로젝트 입력 시스템(Enhanced Input 또는 레거시)을 확인한다.
2. 이동 입력(`MoveForward`, `MoveRight` 또는 동등한 Input Action)을 만든다.
3. Character Blueprint에 입력 이벤트를 연결한다.
4. `Add Movement Input` 노드에 전방/우측 벡터를 연결한다.
5. 플레이 시 캐릭터가 WASD로 이동하는지 확인한다.

## 실수 포인트
- Pawn/Character가 Player Controller에 Possess되지 않아 입력이 들어오지 않는다.
- 입력 축 값 부호를 반대로 연결해 이동 방향이 뒤집힌다.

## 이해 점검 체크리스트
- Action과 Axis 입력의 차이를 설명할 수 있는가?
- Character Blueprint에서 입력 이벤트를 연결했는가?
- Play 모드에서 실제 이동을 확인했는가?

## 다음 학습 추천
- [충돌과 물리](./05-collision-physics.md)

## 셀프 퀴즈
1. 연속적인 이동 값(예: -1~1)을 받는 입력 타입은 무엇인가?
2. 캐릭터가 키 입력을 받지 못할 때 먼저 확인할 설정은 무엇인가?

## 정답
1. Axis Mapping
2. Possess(컨트롤러 소유) 설정

## 확장 과제 (기본/심화)
- 기본: 걷기/달리기 입력 액션을 분리해 속도 전환을 구현한다.
- 심화: 입력 컨텍스트 전환(게임 플레이/메뉴) 구조를 설계해 상태별 입력을 분리한다.

---

[상위 문서로 돌아가기](./README.md)
