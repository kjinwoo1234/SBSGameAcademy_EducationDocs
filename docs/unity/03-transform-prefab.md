# Transform과 Prefab

`Transform`은 위치/회전/크기를 담당하고, `Prefab`은 재사용 가능한 오브젝트 템플릿입니다.

## 학습 목표
- Transform의 Position/Rotation/Scale 역할을 설명할 수 있다.
- Prefab을 생성하고 인스턴스를 배치할 수 있다.
- Apply/Revert 차이를 구분할 수 있다.

## Transform
- Position: 위치
- Rotation: 회전
- Scale: 크기

## Prefab
- 씬 오브젝트를 `Project` 창으로 드래그해 생성
- 프리팹 수정 시 연결된 인스턴스에 반영 가능
- 인스턴스 수정 후 원본에 반영하려면 `Apply`
- 인스턴스 변경을 취소하려면 `Revert`

## 단계별 실습
1. `Enemy` 오브젝트를 씬에 1개 생성합니다.
2. `Project` 창으로 드래그해 `Enemy` Prefab을 만듭니다.
3. Prefab 인스턴스를 씬에 3개 복제 배치합니다.
4. 인스턴스 1개의 색상/스케일을 변경합니다.
5. Inspector의 Overrides를 확인합니다.
6. `Apply All`과 `Revert All`을 각각 실행해 차이를 확인합니다.

## 실수 포인트
- 인스턴스 수정을 원본 수정으로 오해하는 실수
- Prefab 연결이 끊긴(Unpack) 상태를 모르고 작업하는 실수

## 이해 점검 체크리스트
- `Apply`와 `Revert`의 차이를 설명할 수 있는가?
- Prefab 인스턴스 수정이 어디에 반영되는지 알고 있는가?
- Prefab을 재사용 가능한 단위로 설계했는가?

## 다음 학습 추천
- [Scene 관리와 생명주기](./04-scene-lifecycle.md)
- [물리와 충돌 처리](./06-physics-collision.md)

## 셀프 퀴즈
1. Prefab 인스턴스 변경을 원본에 반영하는 기능은 무엇인가?
2. Prefab 인스턴스 변경을 취소하는 기능은 무엇인가?

## 정답
1. Apply
2. Revert

---

[상위 문서로 돌아가기](./README.md)
