# Chapter 04 C# 스크립트 기초

## 학습 목표
- `MonoBehaviour` 생명주기 메서드를 이해한다.
- `Start`/`Update`를 활용해 기본 동작을 구현할 수 있다.

## 세부 주제
### 04-1 스크립트 생성
- 클래스 파일 연결

### 04-2 생명주기
- `Awake`, `Start`, `Update`

### 04-3 변수 노출
- `public`, `[SerializeField]`

## 실습 체크리스트
- 속도를 Inspector에서 조절하는 이동 스크립트를 만든다.

## 본문
### 04-1 스크립트 생성
- C# 스크립트를 만들면 파일명과 클래스명이 같아야 정상 동작합니다.
- 스크립트를 GameObject에 붙여 실행 흐름을 연결합니다.

### 04-2 생명주기
- `Awake`는 초기화, `Start`는 첫 실행, `Update`는 매 프레임 처리에 사용합니다.
- 어떤 로직을 어느 메서드에 둘지 분리하면 버그가 줄어듭니다.

### 04-3 변수 노출
- `public` 또는 `[SerializeField] private`로 Inspector에 값을 노출할 수 있습니다.
- 코드 수정 없이 파라미터를 빠르게 조정할 수 있습니다.

예시 코드:
```csharp
using UnityEngine;

public class MoveForward : MonoBehaviour
{
    [SerializeField] private float speed = 3f;

    void Update()
    {
        transform.Translate(Vector3.forward * speed * Time.deltaTime);
    }
}
```

예상 결과:
```text
오브젝트가 전방으로 이동
```

코드 해석:
- `[SerializeField]`로 `private` 변수도 Inspector에서 조정할 수 있습니다.
- `Update`에서 프레임마다 이동을 적용해 연속 동작을 만듭니다.

연습문제:
1. 문제 1 - 속도 노출
   - 문제: 이동 속도를 Inspector에서 바꿀 수 있게 구현하세요.
   - 입력: speed 값
   - 출력: 이동 속도 변화
   - 조건: `[SerializeField]` 사용
2. 문제 2 - 생명주기 로그
   - 문제: `Awake`, `Start`, `Update` 호출 순서를 로그로 확인하세요.
   - 입력: 없음
   - 출력: 호출 로그
   - 힌트: `Debug.Log`

정답 포인트:
- 생명주기 분리가 Unity 스크립트 품질 핵심
- Inspector 노출로 튜닝 반복 속도 향상

---

[상위 문서로 돌아가기](./README.md)
