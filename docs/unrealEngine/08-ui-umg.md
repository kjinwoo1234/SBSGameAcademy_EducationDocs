# Chapter 08 UI와 UMG

## 학습 목표
- UMG 위젯 시스템의 기본 구조를 이해한다.
- HUD 텍스트/버튼을 게임 로직과 연결할 수 있다.

## 세부 주제
### 08-1 Widget Blueprint
- UI 자산 생성
### 08-2 HUD 표시
- Viewport 추가
### 08-3 이벤트 연결
- 버튼 클릭 처리

## 실습 체크리스트
- 체력/점수 UI를 화면에 표시하고 갱신한다.

## 본문
### 08-1 Widget Blueprint
- UMG는 언리얼 UI를 제작하는 시스템입니다.
### 08-2 HUD 표시
- 게임 시작 시 위젯을 생성해 Viewport에 추가합니다.
### 08-3 이벤트 연결
- 버튼 이벤트를 게임 함수와 연결해 상호작용을 만듭니다.

약어 설명(UMG):
- **UMG**는 **Unreal Motion Graphics**의 약자입니다.
- 언리얼 엔진의 UI 제작 시스템을 의미합니다.
- HUD, 메뉴, 팝업 등 대부분의 화면 UI를 UMG로 구성합니다.

예시 코드(위젯 표시):
```cpp
if (HUDWidgetClass)
{
    UUserWidget* HUD = CreateWidget<UUserWidget>(GetWorld(), HUDWidgetClass);
    HUD->AddToViewport();
}
```

예상 결과:
```text
게임 실행 시 HUD가 표시되고 값 갱신
```

코드 해석:
- 위젯 클래스를 런타임에 생성해 화면(Viewport)에 추가합니다.
- UI 초기화 지점을 명확히 두면 누락 버그를 줄일 수 있습니다.

연습문제:
1. 문제: 체력 텍스트 바인딩
   - 입력: HP 변경
   - 출력: UI 값 갱신
   - 조건: 업데이트 함수 분리
2. 문제: 재시작 버튼 연결
   - 입력: 버튼 클릭
   - 출력: 레벨 재로드
   - 힌트: 레벨 이름 참조

정답 포인트:
- UI는 표시 로직과 게임 로직 분리
- 위젯 참조 null 체크 필수

---

[상위 문서로 돌아가기](./README.md)
