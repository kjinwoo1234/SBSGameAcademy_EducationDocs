# 애니메이션 기초 (Animator)

캐릭터 움직임은 Animator Controller 상태 전환으로 관리합니다.

## 핵심
- Animation Clip: 실제 모션 데이터
- Animator Controller: 상태(State)와 전이(Transition)
- Parameter: 전이 조건 (`isRun`, `Speed` 등)

## 기본 흐름
1. Idle/Run 클립 준비
2. Animator Controller에 상태 생성
3. Parameter 조건으로 Idle <-> Run 전환

---

[상위 문서로 돌아가기](./README.md)
