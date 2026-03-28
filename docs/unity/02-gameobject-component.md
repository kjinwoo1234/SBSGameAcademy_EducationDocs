# GameObject와 Component

Unity에서 모든 오브젝트는 `GameObject`이며, 기능은 `Component`로 붙입니다.

## 핵심
- GameObject: 빈 컨테이너
- Component: 실제 기능 (Transform, Renderer, Rigidbody 등)
- Script도 Component로 동작

## 예시
- Cube + MeshRenderer: 화면 표시
- Cube + Collider: 충돌 판정
- Cube + Rigidbody: 물리 이동

## 실수 포인트
- "스크립트를 만들었는데 동작 안 함" -> 오브젝트에 컴포넌트로 붙였는지 확인

---

[상위 문서로 돌아가기](./README.md)
