# Chapter 07 UI 기초

## 학습 목표
- Canvas 기반 UI 구조를 이해한다.
- 텍스트/버튼 UI를 스크립트와 연결할 수 있다.

## 세부 주제
### 07-1 Canvas 구조
- UI 계층
### 07-2 Text와 Button
- 표시/입력 UI
### 07-3 UI 업데이트
- 점수/체력 반영

## 실습 체크리스트
- 점수 텍스트와 재시작 버튼을 만든다.

## 본문
### 07-1 Canvas 구조
- Unity UI는 Canvas 아래에서 렌더링됩니다.
### 07-2 Text와 Button
- Text는 상태 표시, Button은 사용자 액션 입력에 사용됩니다.
### 07-3 UI 업데이트
- 게임 상태 변화 시 UI 값을 즉시 갱신해야 사용자 피드백이 좋아집니다.

예시 코드:
```csharp
using UnityEngine;
using TMPro;

public class ScoreUI : MonoBehaviour
{
    [SerializeField] TextMeshProUGUI scoreText;
    int score = 0;
    public void AddScore(int value)
    {
        score += value;
        scoreText.text = $"Score: {score}";
    }
}
```

예상 결과:
```text
점수 증가 시 텍스트 즉시 갱신
```

코드 해석:
- `AddScore`에서 점수 상태 변경과 UI 갱신을 한 번에 처리합니다.
- 텍스트 참조가 연결되지 않으면 null 오류가 발생하므로 Inspector 연결이 필요합니다.

연습문제:
1. 문제: 버튼 클릭으로 점수 +1
   - 입력: 버튼 클릭
   - 출력: 점수 텍스트 변경
   - 조건: Button OnClick 연결
2. 문제: 체력 0일 때 게임오버 패널 표시
   - 입력: HP 값
   - 출력: 패널 활성화
   - 힌트: `SetActive(true)`

정답 포인트:
- UI 참조 누락(null) 점검이 중요
- UI 갱신은 상태 변경 시점과 함께 처리

---

[상위 문서로 돌아가기](./README.md)
