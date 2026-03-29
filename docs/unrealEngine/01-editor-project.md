# 언리얼 에디터와 프로젝트 구조

언리얼은 `Content Browser`를 중심으로 에셋을 관리하며, 패널 역할을 이해하면 이후 실습 속도가 크게 올라갑니다.

## 학습 목표
- Unreal Editor의 핵심 패널 역할을 구분할 수 있다.
- 프로젝트 기본 폴더 구조를 직접 설계할 수 있다.
- 레벨 저장/정리 습관을 적용할 수 있다.

## 핵심
- `Viewport`: 레벨 배치와 카메라 이동
- `World Outliner`: 레벨 오브젝트 계층 확인
- `Details`: 선택 오브젝트 속성 편집
- `Content Browser`: 블루프린트/머티리얼/메시 에셋 관리

## 단계별 실습
1. Games 템플릿으로 새 프로젝트를 생성한다.
2. `Content/Blueprints`, `Content/Materials`, `Content/UI` 폴더를 만든다.
3. 빈 레벨을 만들고 큐브를 하나 배치한 뒤 `SM_Cube_Test`로 이름을 바꾼다.
4. 레벨을 `Main`으로 저장한다.

## 실수 포인트
- 에셋을 루트에 바로 생성해 폴더 구조가 빠르게 혼잡해진다.
- 레벨을 저장하지 않아 재실행 시 작업 내용이 사라진다.

## 이해 점검 체크리스트
- [ ] `World Outliner`와 `Content Browser`의 역할 차이를 설명할 수 있는가?
- [ ] 기능별 폴더를 먼저 만들고 에셋을 분류했는가?
- [ ] 레벨 파일을 이름 지정 후 저장했는가?

## 다음 학습 추천
- [Actor와 Component](./02-actor-component.md)

## 셀프 퀴즈
1. 레벨 오브젝트 목록을 확인하는 패널은 무엇인가?
2. 블루프린트와 머티리얼 파일을 관리하는 패널은 무엇인가?

## 정답
1. World Outliner
2. Content Browser

---

[상위 문서로 돌아가기](./README.md)
