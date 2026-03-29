# UMG UI 기초

언리얼 UI는 UMG(User Widget) 기반으로 제작하며, 위젯 생성과 Viewport 추가 흐름을 이해하면 대부분의 HUD/메뉴를 만들 수 있습니다.

## 학습 목표
- Widget Blueprint를 생성하고 기본 UI를 구성할 수 있다.
- `Add to Viewport`를 통해 UI를 화면에 표시할 수 있다.
- 버튼 클릭 이벤트로 간단한 상호작용을 구현할 수 있다.

## 핵심
- `Widget Blueprint`: UI 레이아웃과 로직을 담는 단위
- `Add to Viewport`: 위젯을 화면에 표시하는 호출
- `OnClicked`: 버튼 상호작용 이벤트

## 단계별 실습
1. `WBP_MainHUD` 위젯을 생성한다.
2. Text와 Button을 배치하고 버튼 라벨을 설정한다.
3. 캐릭터 BeginPlay에서 `Create Widget` -> `Add to Viewport`를 연결한다.
4. 버튼 `OnClicked` 이벤트에 `Print String`을 연결한다.

## 실수 포인트
- 위젯을 생성만 하고 Viewport에 추가하지 않아 화면에 보이지 않는다.
- 클릭이 안 될 때 입력 모드(UI Only/Game and UI) 설정을 확인하지 않는다.

## 이해 점검 체크리스트
- [ ] Widget Blueprint를 직접 생성했는가?
- [ ] BeginPlay에서 UI 생성/표시 흐름을 구현했는가?
- [ ] 버튼 클릭 이벤트가 실제로 호출되는가?

## 다음 학습 추천
- [애니메이션 블루프린트 기초](./07-animation-blueprint.md)

## 셀프 퀴즈
1. UI를 화면에 표시할 때 쓰는 대표 노드는 무엇인가?
2. 버튼 클릭 시 연결하는 대표 이벤트는 무엇인가?

## 정답
1. Add to Viewport
2. OnClicked

---

[상위 문서로 돌아가기](./README.md)
