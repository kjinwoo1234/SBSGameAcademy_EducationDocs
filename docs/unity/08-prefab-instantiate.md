# Chapter 08 프리팹과 오브젝트 생성

## 학습 목표
- 프리팹의 목적과 사용 방법을 이해한다.
- 런타임에 오브젝트를 생성/제거할 수 있다.

## 세부 주제
### 08-1 프리팹 개념
- 재사용 가능한 오브젝트 원형
### 08-2 Instantiate
- 런타임 생성
### 08-3 Destroy
- 오브젝트 제거

## 실습 체크리스트
- 적 프리팹을 주기적으로 생성하고 제거한다.

## 본문
### 08-1 프리팹 개념
- 프리팹은 설정된 오브젝트를 자산으로 저장해 재사용하는 기능입니다.
### 08-2 Instantiate
- 게임 실행 중 동일 오브젝트를 여러 번 생성할 때 사용합니다.
### 08-3 Destroy
- 사용이 끝난 오브젝트는 제거해 성능/메모리를 관리합니다.

예시 코드:
```csharp
using UnityEngine;

public class Spawner : MonoBehaviour
{
    [SerializeField] GameObject enemyPrefab;
    void Start()
    {
        Instantiate(enemyPrefab, Vector3.zero, Quaternion.identity);
    }
}
```

예상 결과:
```text
게임 시작 시 적 오브젝트 1개 생성
```

코드 해석:
- `Instantiate`는 프리팹을 월드에 복제 생성합니다.
- 위치/회전값을 전달해 원하는 지점에 스폰할 수 있습니다.

연습문제:
1. 문제: 2초마다 적 생성
   - 입력: 시간 경과
   - 출력: 적 오브젝트 누적 생성
   - 조건: `InvokeRepeating` 또는 코루틴 사용
2. 문제: 5초 뒤 자동 제거
   - 입력: 생성된 오브젝트
   - 출력: 일정 시간 후 제거
   - 힌트: `Destroy(obj, 5f)`

정답 포인트:
- 프리팹화로 반복 설정 비용 감소
- 생성/제거 균형이 성능 관리 핵심

---

[상위 문서로 돌아가기](./README.md)
