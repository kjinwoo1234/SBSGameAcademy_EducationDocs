# Actor와 Component

언리얼의 기본 배치 단위는 `Actor`이며, 기능은 여러 Component를 조합해 확장합니다.

## 학습 목표
- Actor와 Component의 역할 차이를 설명할 수 있다.
- 자주 사용하는 Component를 목적에 맞게 추가할 수 있다.
- 월드 배치와 실행 흐름의 관계를 이해할 수 있다.

## 핵심
- `Actor`: 월드에 배치되는 기본 단위
- `Scene Component`: 위치/회전/스케일과 계층 구조 관리
- `Static Mesh Component`: 3D 모델 표시
- `Collision Component`: 충돌 범위/이벤트 처리

## 단계별 실습
1. Actor Blueprint(`BP_Item`)를 생성한다.
2. `Static Mesh Component`를 추가해 메시를 지정한다.
3. `Sphere Collision`을 추가하고 범위를 조절한다.
4. 레벨에 `BP_Item`을 배치하고 Play로 동작을 확인한다.

## 실수 포인트
- 블루프린트는 만들었지만 레벨에 배치하지 않아 실행되지 않는다.
- 루트 컴포넌트와 자식 컴포넌트 관계를 잘못 설정해 위치가 어긋난다.

## 이해 점검 체크리스트
- [ ] Actor와 Component의 책임을 각각 설명할 수 있는가?
- [ ] 최소 2개 이상의 Component를 Actor에 추가했는가?
- [ ] 레벨 배치 후 Play 모드에서 객체를 확인했는가?

## 다음 학습 추천
- [Level과 Blueprint 기초](./03-level-blueprint.md)

## 셀프 퀴즈
1. 월드에 배치되는 기본 단위는 무엇인가?
2. 충돌 범위를 잡는 데 자주 쓰는 컴포넌트는 무엇인가?

## 정답
1. Actor
2. Collision Component(예: Sphere Collision)

---

[상위 문서로 돌아가기](./README.md)
