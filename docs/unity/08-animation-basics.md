# 애니메이션 기초 (Animator)

캐릭터 움직임은 Animator Controller 상태 전환으로 관리합니다.

## 학습 목표
- Animation Clip, Animator Controller, Parameter 역할을 설명할 수 있다.
- Idle/Run 상태 전이를 직접 구성할 수 있다.
- 상태 전환이 안 될 때 원인을 점검할 수 있다.

## 핵심
- Animation Clip: 실제 모션 데이터
- Animator Controller: 상태(State)와 전이(Transition)
- Parameter: 전이 조건 (`isRun`, `Speed` 등)

## 기본 흐름
1. Idle/Run 클립 준비
2. Animator Controller에 상태 생성
3. Parameter 조건으로 Idle <-> Run 전환

## 미니 실습 과제
1. `Speed` float 파라미터를 추가합니다.
2. Idle -> Run 전이 조건을 `Speed > 0.1`로 설정합니다.
3. Run -> Idle 전이 조건을 `Speed <= 0.1`로 설정합니다.
4. 이동 스크립트에서 `animator.SetFloat("Speed", moveMagnitude)`를 갱신합니다.
5. Play 모드에서 이동 입력 시 상태 전환을 확인합니다.

## 전체 코드 예시 (복붙 실행용)
```csharp
using UnityEngine;

public class PlayerAnimationController : MonoBehaviour
{
    [SerializeField] private Animator animator;
    [SerializeField] private float speed = 5f;

    private void Update()
    {
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");
        Vector3 dir = new Vector3(h, 0f, v);

        transform.Translate(dir * speed * Time.deltaTime, Space.World);
        animator.SetFloat("Speed", dir.magnitude);
    }
}
```

## 실수 포인트
- 파라미터 이름 오타로 전이가 동작하지 않는 실수
- Transition 조건은 맞지만 `Has Exit Time` 때문에 전이가 지연되는 실수

## 이해 점검 체크리스트
- 상태 전이 조건이 양방향으로 설정되었는가?
- 파라미터 이름이 코드와 Animator에서 일치하는가?
- Play 모드에서 현재 상태를 Animator 창에서 확인했는가?

## 다음 학습 추천
- [데이터 저장 (PlayerPrefs, JSON)](./09-data-save.md)

## 셀프 퀴즈
1. 상태 전환 조건을 코드에서 제어하기 위한 Animator 요소는 무엇인가?
2. Idle/Run 전환에서 흔히 사용하는 float 파라미터 이름 예시는 무엇인가?

## 정답
1. Parameter
2. `Speed`

---

[상위 문서로 돌아가기](./README.md)
