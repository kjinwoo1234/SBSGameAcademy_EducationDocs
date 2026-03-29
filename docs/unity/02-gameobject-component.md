# GameObject와 Component

Unity에서 모든 오브젝트는 `GameObject`이며, 기능은 `Component`를 붙여서 확장합니다.

## 학습 목표
- GameObject와 Component의 역할 차이를 설명할 수 있다.
- 오브젝트에 Component를 추가하고 동작을 직접 확인할 수 있다.
- 스크립트가 동작하지 않을 때 기본 점검 순서를 수행할 수 있다.

## 핵심 개념
- GameObject: 오브젝트를 담는 기본 단위(컨테이너)
- Component: 오브젝트 기능을 담당하는 모듈 (`Transform`, `Renderer`, `Rigidbody` 등)
- Script도 Component로 동작
- **오개념 방지**: 모든 GameObject는 최소 1개의 `Transform`을 반드시 가진다.

## 예시
- Cube + MeshRenderer: 화면 표시
- Cube + Collider: 충돌 판정
- Cube + Rigidbody: 물리 이동

## 단계별 실습 (에디터 따라하기)
1. `Hierarchy`에서 `3D Object > Cube`를 생성하고 이름을 `Player`로 변경합니다.
2. `Inspector`에서 현재 Component(`Transform`, `Mesh Filter`, `Mesh Renderer`, `Box Collider`)를 확인합니다.
3. `Add Component` 버튼으로 `Rigidbody`를 추가합니다.
4. `PlayerController` 스크립트를 만들고 `Player` 오브젝트에 붙입니다.
5. 스크립트의 `Update()`에 `Debug.Log("Player Update");`를 작성합니다.
6. `Play` 버튼을 눌러 Console 로그와 중력 반응을 확인합니다.
7. `Rigidbody`의 `Use Gravity`를 켜고/끄며 동작 차이를 비교합니다.

## 실수 포인트
- "스크립트를 만들었는데 동작 안 함" -> 오브젝트에 컴포넌트로 붙였는지 확인
- Play 모드가 아닌 상태에서 동작을 기대하는 실수
- Script 파일명과 클래스명이 달라 부착이 안 되는 실수

## 이해 점검 체크리스트
- [ ] GameObject와 Component의 차이를 한 문장으로 설명할 수 있는가?
- [ ] 모든 GameObject에 `Transform`이 기본 포함된다는 점을 알고 있는가?
- [ ] `Add Component` 후 Play 모드에서 실제 동작을 확인했는가?

## 셀프 퀴즈
1. GameObject에 기능을 부여하는 단위는 무엇인가?
2. Rigidbody를 추가했는데 낙하하지 않는다면 무엇을 먼저 확인해야 하는가?
3. 스크립트가 실행되지 않을 때 가장 먼저 확인할 항목 2가지는 무엇인가?

## 정답
1. Component
2. `Use Gravity` 및 Rigidbody/Collider 설정
3. 오브젝트에 스크립트가 부착되었는지, Play 모드인지 여부

## 다음 학습 추천
- [Transform과 Prefab](./03-transform-prefab.md)
- [물리와 충돌 처리](./06-physics-collision.md)

---

[상위 문서로 돌아가기](./README.md)
