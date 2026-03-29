# Level과 Blueprint 기초

Blueprint는 언리얼의 비주얼 스크립팅 도구이며, 범위에 따라 Level Blueprint와 Actor Blueprint를 구분해 써야 유지보수가 쉬워집니다.

## 학습 목표
- Level Blueprint와 Actor Blueprint의 사용 범위를 구분할 수 있다.
- 이벤트 기반 노드 실행 흐름을 설명할 수 있다.
- 재사용 가능한 로직 분리 기준을 적용할 수 있다.

## 핵심
- `Level Blueprint`: 현재 레벨 전용 로직
- `Actor Blueprint`: 재사용 가능한 오브젝트 로직
- 기본 실행 흐름: `Event BeginPlay` -> 노드 실행 -> 상태/출력 변경

## 단계별 실습
1. 레벨 블루프린트에서 `Event BeginPlay`에 `Print String`을 연결한다.
2. Actor Blueprint(`BP_Door`)를 생성해 열림/닫힘 로직을 만든다.
3. 레벨에 `BP_Door`를 여러 개 배치해 재사용성을 확인한다.
4. 전역성 로직은 레벨 블루프린트가 아닌 별도 클래스에 둘 항목을 구분한다.

## 실수 포인트
- 재사용 로직을 Level Blueprint에 몰아넣어 다음 레벨에서 재작업이 발생한다.
- 이벤트 노드 실행 연결선이 끊겨도 오류 없이 동작하지 않아 원인 파악이 늦어진다.

## 이해 점검 체크리스트
- [ ] Level/Actor Blueprint의 차이를 사례로 설명할 수 있는가?
- [ ] `BeginPlay` 이벤트 흐름을 직접 구성했는가?
- [ ] 재사용 로직을 Actor Blueprint로 분리했는가?

## 다음 학습 추천
- [입력 처리와 캐릭터 이동](./04-input-movement.md)

## 셀프 퀴즈
1. 한 레벨에서만 쓰는 임시 연출 로직은 어디에 두는 것이 적절한가?
2. 여러 레벨에서 재사용할 문 열기 로직은 어디에 두는 것이 적절한가?

## 정답
1. Level Blueprint
2. Actor Blueprint

---

[상위 문서로 돌아가기](./README.md)
