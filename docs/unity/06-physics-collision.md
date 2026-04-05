# Chapter 06 물리와 충돌

## 학습 목표
- Rigidbody/Collider 역할을 구분한다.
- 충돌/트리거 이벤트를 처리할 수 있다.

## 세부 주제
### 06-1 물리 구성 요소
- Rigidbody, Collider
### 06-2 충돌 이벤트
- `OnCollisionEnter`
### 06-3 트리거 이벤트
- `OnTriggerEnter`

## 실습 체크리스트
- 벽 충돌과 아이템 트리거를 각각 구현한다.

## 본문
### 06-1 물리 구성 요소
- Rigidbody는 물리 연산 대상, Collider는 충돌 영역을 정의합니다.
### 06-2 충돌 이벤트
- 실제 물리 충돌이 일어나면 Collision 이벤트가 호출됩니다.
### 06-3 트리거 이벤트
- Trigger는 막지 않고 "겹침 감지"만 수행합니다.

예시 코드:
```csharp
using UnityEngine;

public class HitLog : MonoBehaviour
{
    void OnCollisionEnter(Collision collision)
    {
        Debug.Log("충돌: " + collision.gameObject.name);
    }
}
```

예상 결과:
```text
충돌 대상 이름 로그 출력
```

코드 해석:
- `OnCollisionEnter`는 물리 충돌이 발생한 순간 호출됩니다.
- `collision.gameObject.name`으로 충돌 대상 식별이 가능합니다.

연습문제:
1. 문제: 플레이어가 코인을 트리거로 먹으면 점수 +1
   - 입력: 트리거 충돌
   - 출력: 점수 증가
   - 조건: 코인 오브젝트 비활성화
2. 문제: 벽 충돌 시 효과음 재생
   - 입력: 충돌 이벤트
   - 출력: 소리 재생
   - 힌트: `AudioSource.Play()`

정답 포인트:
- 충돌과 트리거 용도를 분리
- Collider 설정(Is Trigger) 확인 필수

---

[상위 문서로 돌아가기](./README.md)
