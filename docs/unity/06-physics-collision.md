# 물리와 충돌 처리

Unity 물리는 `Rigidbody` + `Collider` 조합으로 처리합니다.

## 핵심
- Rigidbody: 물리 시뮬레이션 적용
- Collider: 충돌 범위
- Trigger: 물리 반응 없이 이벤트만 발생

## 자주 쓰는 이벤트
- `OnCollisionEnter(Collision col)`
- `OnTriggerEnter(Collider other)`

## 실수 포인트
- 충돌 이벤트가 안 뜰 때: Collider/Rigidbody 조합 확인

---

[상위 문서로 돌아가기](./README.md)
