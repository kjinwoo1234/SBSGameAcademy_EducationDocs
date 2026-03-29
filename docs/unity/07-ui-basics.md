# UI 기초 (Canvas, Text, Button)

Unity UI는 Canvas 아래에 배치됩니다.

## 학습 목표
- Canvas 기반 UI 구조를 설명할 수 있다.
- 버튼 클릭 이벤트를 연결하고 동작시킬 수 있다.
- 해상도 대응(Canvas Scaler)을 점검할 수 있다.

## 기본 구성
- Canvas: UI 루트
- Text (TMP): 텍스트 표시
- Button: 클릭 입력

## 버튼 이벤트 연결
1. 버튼 선택
2. Inspector의 `On Click()`에 함수 연결
3. 대상 오브젝트 + 메서드 지정

## 미니 실습 과제
1. Text(TMP)와 Button을 생성합니다.
2. `UIController` 스크립트에 텍스트 참조 변수를 선언합니다.
3. 버튼 `On Click()`에서 `ChangeText()` 함수를 연결합니다.
4. 클릭 시 텍스트를 `"Start Clicked"`로 변경합니다.

## 전체 코드 예시 (복붙 실행용)
```csharp
using TMPro;
using UnityEngine;

public class UIController : MonoBehaviour
{
    [SerializeField] private TMP_Text label;

    public void ChangeText()
    {
        label.text = "Start Clicked";
    }
}
```

## 실수 포인트
- 화면 해상도 대응: Canvas Scaler 설정 확인 (`Scale With Screen Size`)
- TMP Text 대신 Legacy Text를 혼용해 폰트 깨짐이 발생하는 실수

## 이해 점검 체크리스트
- 버튼 클릭 이벤트가 정상 호출되는가?
- 텍스트 참조가 Inspector에서 연결되었는가?
- Canvas Scaler가 해상도 대응 모드인가?

## 다음 학습 추천
- [애니메이션 기초 (Animator)](./08-animation-basics.md)

## 셀프 퀴즈
1. Unity UI가 기본적으로 배치되는 루트 오브젝트는 무엇인가?
2. Button 클릭 시 함수를 연결하는 Inspector 항목 이름은 무엇인가?

## 정답
1. Canvas
2. `On Click()`

---

[상위 문서로 돌아가기](./README.md)
