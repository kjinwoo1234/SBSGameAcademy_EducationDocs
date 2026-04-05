# Chapter 03 Scene-GameObject-Component 구조

## 학습 목표
- Unity의 핵심 구조(Scene-GameObject-Component)를 이해한다.
- 컴포넌트 추가/제거를 통해 동작을 구성할 수 있다.

## 세부 주제
### 03-1 GameObject
- 씬의 기본 단위

### 03-2 Component
- 기능 조합 방식

### 03-3 Transform
- 위치/회전/크기

## 실습 체크리스트
- 오브젝트에 컴포넌트를 추가해 동작 차이를 확인한다.

## 본문
### 03-1 GameObject
- Unity에서 화면에 존재하는 모든 대상은 GameObject로 표현됩니다.
- GameObject 자체는 빈 껍데기이며 컴포넌트가 기능을 만듭니다.

### 03-2 Component
- Renderer, Collider, Script 같은 컴포넌트를 붙여 기능을 조립합니다.
- 코드보다 에디터 조립이 먼저라는 점이 Unity의 큰 특징입니다.

### 03-3 Transform
- 모든 GameObject는 Transform을 필수로 가집니다.
- 좌표/회전/크기를 담당하는 가장 기본 컴포넌트입니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| GameObject | 씬에 존재하는 엔티티 기본 단위 |
| Component | GameObject에 기능을 부여하는 모듈 |

예시 코드:
```csharp
using UnityEngine;

public class Spin : MonoBehaviour
{
    void Update()
    {
        transform.Rotate(0f, 60f * Time.deltaTime, 0f);
    }
}
```

예상 결과:
```text
오브젝트가 Y축 기준으로 회전
```

코드 해석:
- `Update()`는 매 프레임 실행됩니다.
- `Time.deltaTime`을 곱해 프레임 속도 차이를 보정합니다.

연습문제:
1. 문제 1 - 컴포넌트 조합
   - 문제: 큐브에 Collider와 Rigidbody를 붙여 물리 반응을 확인하세요.
   - 입력: 없음
   - 출력: Play 시 물리 반응
   - 조건: 컴포넌트 on/off 비교
2. 문제 2 - 회전 스크립트
   - 문제: 오브젝트가 3축 중 1축으로 계속 회전하도록 작성하세요.
   - 입력: 회전 속도
   - 출력: 회전 동작
   - 힌트: `transform.Rotate`

정답 포인트:
- Unity는 기능을 컴포넌트로 조합하는 구조
- Transform 이해가 공간 제어의 출발점

---

[상위 문서로 돌아가기](./README.md)
