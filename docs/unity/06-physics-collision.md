# 물리와 충돌 처리

Unity 물리는 `Rigidbody` + `Collider` 조합으로 처리합니다.

## 학습 목표
- Collision과 Trigger의 차이를 설명할 수 있다.
- 충돌 이벤트가 호출되지 않을 때 점검 순서를 수행할 수 있다.
- Rigidbody/Collider 조합을 올바르게 적용할 수 있다.

## 핵심
- Rigidbody: 물리 시뮬레이션 적용
- Collider: 충돌 범위
- Trigger: 물리 반응 없이 이벤트만 발생

## 자주 쓰는 이벤트
- `OnCollisionEnter(Collision col)`
- `OnTriggerEnter(Collider other)`

## 실수 포인트
- 충돌 이벤트가 안 뜰 때: Collider/Rigidbody 조합 확인
- Trigger를 켜놓고 Collision 이벤트를 기다리는 실수
- 레이어 충돌 매트릭스 설정을 확인하지 않는 실수

## 단계별 실습
1. `Player`와 `Item` 오브젝트를 생성합니다.
2. 두 오브젝트에 Collider를 추가합니다.
3. `Player`에 Rigidbody를 추가합니다.
4. `Item` Collider의 `Is Trigger`를 활성화합니다.
5. `OnTriggerEnter` 로그 코드를 작성하고 충돌을 테스트합니다.
6. `Is Trigger`를 비활성화 후 `OnCollisionEnter`로 동작 차이를 확인합니다.

## 이해 점검 체크리스트
- [ ] Trigger/Collision 이벤트를 구분해 사용했는가?
- [ ] 충돌 대상 중 최소 하나에 Rigidbody가 있는가?
- [ ] Layer Collision Matrix 설정을 확인했는가?

## 다음 학습 추천
- [UI 기초 (Canvas, Text, Button)](./07-ui-basics.md)
- [데이터 저장 (PlayerPrefs, JSON)](./09-data-save.md)

## 셀프 퀴즈
1. 물리 반응 없이 충돌 감지만 하고 싶을 때 사용하는 방식은?
2. Trigger 이벤트가 호출되지 않을 때 필수 점검 항목 하나는?

## 정답
1. Trigger (`Is Trigger` 활성화)
2. 충돌 대상 중 최소 하나에 Rigidbody 존재 여부

---

[상위 문서로 돌아가기](./README.md)
