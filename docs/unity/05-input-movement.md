# 입력 처리와 캐릭터 이동

키보드/마우스 입력을 받아 플레이어를 이동시키는 파트입니다.

## 학습 목표
- `Input.GetAxis` 기반 이동 코드를 작성할 수 있다.
- 프레임 독립 이동(`deltaTime`)을 적용할 수 있다.
- 이동 속도/방향 버그를 스스로 점검할 수 있다.

## 기본 입력
- `Input.GetKey(KeyCode.W)`
- `Input.GetAxis("Horizontal")`, `Input.GetAxis("Vertical")`

## 이동 예제
```csharp
float h = Input.GetAxis("Horizontal");
float v = Input.GetAxis("Vertical");
Vector3 dir = new Vector3(h, 0, v);
transform.Translate(dir * speed * Time.deltaTime);
```

## 실수 포인트
- `Time.deltaTime`를 곱하지 않으면 프레임에 따라 속도가 달라짐
- 로컬 좌표/월드 좌표 기준을 혼동하는 실수
- 입력 축 이름 오타 (`Horizontal`, `Vertical`) 실수

## 단계별 실습
1. `Player` 오브젝트에 `PlayerMove` 스크립트를 붙입니다.
2. `speed` 변수를 `public`으로 노출합니다.
3. `GetAxis`로 `h`, `v` 입력을 받습니다.
4. `Translate`로 이동을 구현합니다.
5. 속도를 2, 5, 10으로 바꿔 체감 차이를 확인합니다.
6. `deltaTime` 유무에 따른 움직임 차이를 비교합니다.

## 이해 점검 체크리스트
- `deltaTime`를 이동 계산에 사용했는가?
- 입력 축 이름이 정확한가?
- 이동 방향 벡터가 의도대로 계산되는가?

## 다음 학습 추천
- [물리와 충돌 처리](./06-physics-collision.md)
- [애니메이션 기초 (Animator)](./08-animation-basics.md)

## 셀프 퀴즈
1. 프레임 독립 이동을 위해 곱해야 하는 값은 무엇인가?
2. 좌우/앞뒤 입력 축 기본 이름은 무엇인가?

## 정답
1. `Time.deltaTime`
2. `Horizontal`, `Vertical`

## 확장 과제 (기본/심화)
- 기본: 대각선 이동 속도 보정(정규화)을 적용한다.
- 심화: 키보드/패드 입력을 분리해 입력 장치별 민감도 옵션을 추가한다.

---

[상위 문서로 돌아가기](./README.md)
