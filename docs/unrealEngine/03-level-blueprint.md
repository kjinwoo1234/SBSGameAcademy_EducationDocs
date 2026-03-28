# Level과 Blueprint 기초

Blueprint는 언리얼의 비주얼 스크립팅 도구입니다.

## 구분
- Level Blueprint: 현재 레벨 전용 로직
- Actor Blueprint: 재사용 가능한 오브젝트 로직

## 기본 흐름
`Event BeginPlay` -> 로직 노드 실행 -> 결과 출력/상태 변경

## 권장
- 전역성 로직은 GameMode/GameInstance로 분리
- 재사용 로직은 Actor Blueprint로 분리

---

[상위 문서로 돌아가기](./README.md)
