# Chapter 25 비동기 심화

## 학습 목표
- 비동기 작업의 병렬 실행과 취소를 안전하게 다룰 수 있다.
- `Task.WhenAll`, `CancellationToken`을 사용한 실전 패턴을 이해한다.

## 세부 주제
### 25-1 다중 비동기 작업 조합
- `Task.WhenAll`, 실패 전파

### 25-2 취소 토큰
- `CancellationToken`, `CancellationTokenSource`

### 25-3 실수 포인트와 방어 패턴
- 예외 처리, 타임아웃, 과도한 동시성

## 실습 체크리스트
- 비동기 작업 3개를 병렬 실행하고 취소/예외를 처리한다.

## 본문

### 25-1 다중 비동기 작업 조합
- `Task.WhenAll`은 여러 작업을 동시에 기다립니다.
- 작업 중 하나가 실패하면 예외가 전파되므로 `try-catch`가 필요합니다.

### 25-2 취소 토큰
- `CancellationTokenSource`는 취소 신호를 발행합니다.
- 각 작업은 `token.ThrowIfCancellationRequested()` 또는 토큰 전달 API로 취소에 협조해야 합니다.

### 25-3 실수 포인트와 방어 패턴
- 취소 토큰을 만들고 전달하지 않으면 취소가 동작하지 않습니다.
- 병렬 작업 수가 너무 많으면 오히려 성능이 떨어질 수 있어 제한 전략이 필요합니다.

약어 설명(TAP):
- **TAP**는 **Task-based Asynchronous Pattern**의 약자입니다.
- 한국어로는 "Task 기반 비동기 패턴"이며, C# 비동기 프로그래밍의 표준 모델입니다.
- `async/await`, `Task`, `CancellationToken`이 TAP의 핵심 구성입니다.

비교 표:
| 항목 | 목적 | 장점 | 주의점 |
|---|---|---|---|
| `Task.WhenAll` | 여러 작업 동시 대기 | 총 처리 시간 단축 | 일부 실패 시 전체 예외 처리 필요 |
| `CancellationToken` | 작업 취소 제어 | 사용자 취소/타임아웃 대응 | 토큰 전달 누락 시 무효 |
| `Task.Delay` + 취소 | 대기 중 취소 | 반응성 향상 | 예외 흐름 점검 필요 |

예시 코드:
```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static async Task<int> WorkAsync(int value, int delayMs, CancellationToken token)
    {
        await Task.Delay(delayMs, token);
        token.ThrowIfCancellationRequested();
        return value * 2;
    }

    static async Task Main()
    {
        using var cts = new CancellationTokenSource();
        cts.CancelAfter(500); // 0.5초 뒤 자동 취소

        try
        {
            Task<int> t1 = WorkAsync(10, 200, cts.Token);
            Task<int> t2 = WorkAsync(20, 400, cts.Token);
            Task<int> t3 = WorkAsync(30, 800, cts.Token);

            int[] results = await Task.WhenAll(t1, t2, t3);
            Console.WriteLine($"합계: {results[0] + results[1] + results[2]}");
        }
        catch (OperationCanceledException)
        {
            Console.WriteLine("작업이 취소되었습니다.");
        }
    }
}
```

예상 출력:
```text
작업이 취소되었습니다.
```

코드 해석:
- 세 작업 중 `t3`는 800ms가 필요하지만 `CancelAfter(500)` 때문에 취소됩니다.
- `Task.WhenAll` 대기 중 취소 예외가 발생하며 `OperationCanceledException`으로 처리됩니다.

연습문제:
1. 문제 1 - 병렬 다운로드 시뮬레이터
   - 문제: 작업 5개를 병렬 실행하고 완료 시간을 출력하세요.
   - 입력: 작업별 지연 시간
   - 출력: 각 작업 결과와 총 소요 시간
   - 조건: `Task.WhenAll` 사용
2. 문제 2 - 사용자 취소 처리
   - 문제: 키 입력(`q`) 시 진행 중 작업을 취소하세요.
   - 입력: 콘솔 키 입력
   - 출력: 취소 또는 완료 메시지
   - 조건: `CancellationTokenSource` 사용
3. 문제 3 - 실패/취소 분기 처리
   - 문제: 일부 작업은 예외, 일부 작업은 취소되는 상황을 분리 처리하세요.
   - 입력: 혼합 작업 목록
   - 출력: 실패 건수, 취소 건수
   - 조건: `try-catch` 분기

정답 포인트:
- 비동기 심화의 핵심은 "동시 실행"보다 "실패/취소 제어"
- 취소 가능한 설계를 기본값으로 두면 실전 안정성이 크게 올라감

---

[상위 문서로 돌아가기](./README.md)
