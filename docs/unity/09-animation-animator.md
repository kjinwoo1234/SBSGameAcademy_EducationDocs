# Chapter 09 애니메이션과 Animator

## 학습 목표
- Animator Controller와 상태 전환 개념을 이해한다.
- 파라미터 기반으로 애니메이션을 전환할 수 있다.

## 세부 주제
### 09-1 애니메이션 클립
- 동작 데이터
### 09-2 Animator 상태
- 상태머신
### 09-3 파라미터 전환
- bool/float/trigger

## 실습 체크리스트
- Idle/Run 상태를 속도 값으로 전환한다.

## 본문
### 09-1 애니메이션 클립
- 클립은 시간 축에 따른 동작 데이터입니다.
### 09-2 Animator 상태
- 상태머신은 "어떤 상태에서 어떤 조건으로 이동할지"를 정의합니다.
### 09-3 파라미터 전환
- 스크립트에서 파라미터를 변경해 상태 전환을 제어합니다.

예시 코드:
```csharp
using UnityEngine;

public class AnimControl : MonoBehaviour
{
    [SerializeField] Animator animator;
    void Update()
    {
        float speed = Mathf.Abs(Input.GetAxis("Vertical"));
        animator.SetFloat("Speed", speed);
    }
}
```

예상 결과:
```text
입력 속도에 따라 Idle <-> Run 전환
```

코드 해석:
- 이동 축 값을 속도 파라미터로 변환해 Animator에 전달합니다.
- 상태 전환 조건이 `Speed`를 기준으로 평가되어 애니메이션이 바뀝니다.

연습문제:
1. 문제: 점프 버튼으로 Jump 트리거 발동
   - 입력: Space 키
   - 출력: Jump 애니메이션
   - 조건: Trigger 파라미터 사용
2. 문제: 공격 애니메이션 쿨타임 적용
   - 입력: 공격 키
   - 출력: 일정 간격 공격 전환
   - 힌트: 타이머 변수 사용

정답 포인트:
- 전환 조건은 단순할수록 디버깅이 쉬움
- 파라미터 이름 오타가 흔한 오류 원인

---

[상위 문서로 돌아가기](./README.md)
