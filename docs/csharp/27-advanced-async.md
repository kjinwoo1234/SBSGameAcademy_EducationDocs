# Chapter 27 비동기 심화

## 학습 목표
- 비동기 작업의 병렬 실행과 취소를 안전하게 다룰 수 있다.
- `Task.WhenAll`, `CancellationToken`을 사용한 실전 패턴을 이해한다.

## 본문

### 27-1 다중 비동기 작업 조합

**`Task.WhenAll`**은 여러 `Task`를 **함께 기다립니다**. 모두 성공하면 결과 배열을 받고, **하나라도 실패·취소**되면 예외가 올라오므로 `try-catch`가 필요합니다. (이것이 **TAP**—Task-based Asynchronous Pattern—의 조합 패턴입니다.)

### 27-2 취소 토큰

**`CancellationTokenSource`**가 취소 신호를 만들고, 각 작업에 **`CancellationToken`**을 넘깁니다. `CancelAfter`·`Cancel`로 신호를 주면, 작업은 `ThrowIfCancellationRequested` 또는 토큰을 받는 API(`Task.Delay(..., token)`)로 **협조해 중단**해야 합니다. 토큰을 안 넘기면 취소가 무효입니다.

### 27-3 실수 포인트와 방어 패턴

토큰을 만들어 놓고 전달을 잊는 실수가 흔합니다. 또한 작업을 무작정 많이 병렬로 열면 오히려 느려질 수 있어, 입문에서는 **소수 Task + WhenAll + 취소/예외 처리**부터 익힙니다.

| 항목 | 목적 | 장점 | 주의점 |
|---|---|---|---|
| `Task.WhenAll` | 여러 작업 동시 대기 | 총 처리 시간 단축 | 일부 실패 시 전체 예외 처리 필요 |
| `CancellationToken` | 작업 취소 제어 | 사용자 취소/타임아웃 대응 | 토큰 전달 누락 시 무효 |
| `Task.Delay` + 취소 | 대기 중 취소 | 반응성 향상 | 예외 흐름 점검 필요 |

아래 코드를 실행해 `CancelAfter(500)` 때문에 취소 메시지가 나오는지 확인해 보세요.

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

**예상 출력**

```text
작업이 취소되었습니다.
```

**코드 해석**
- `t3`는 800ms가 필요하지만 500ms에 취소 신호가 갑니다.
- `WhenAll` 대기 중 `OperationCanceledException`이 나고 catch에서 메시지를 출력합니다.
- 토큰을 `Delay`와 `ThrowIfCancellationRequested`에 넘긴 것이 핵심입니다.

### 연습문제

**문제 1**
- 문제: 작업 3~5개를 병렬 실행하고 결과를 출력하세요.
- 입력: 없음(지연 ms를 코드에 고정)
- 출력: 각 결과 또는 합계 1줄 + (선택) 총 소요에 대한 메모
- 조건: `Task.WhenAll` 사용, 취소 없이 완료되게 Delay 합이 Cancel보다 짧게

**문제 2**
- 문제: 진행 중 작업을 취소하세요.
- 입력: 없음 — `CancelAfter(300)` **또는** 키 `q` 입력 시 `Cancel()`
- 출력: `취소됨`
- 조건: `CancellationTokenSource` + 토큰을 작업에 전달

**문제 3**
- 문제: 실패와 취소를 구분해 처리하세요.
- 입력: 없음(한 Task는 `throw`, 한 Task는 취소)
- 출력: `실패:1` / `취소:1` 형태(집계 방식 자유, 두 종류를 구분할 것)
- 조건: `try-catch`에서 `OperationCanceledException`과 그 외 예외 분기

### 정답 포인트

- 문제 1: 여러 `WorkAsync` 시작 후 `await Task.WhenAll(...)`
- 문제 2: `cts.Token`을 Delay에 전달 + Cancel/CancelAfter
- 문제 3: cancel은 `OperationCanceledException`, 그 외는 실패로 카운트
- 공통: 동시 실행보다 실패/취소 제어가 심화의 핵심

---

[상위 문서로 돌아가기](./README.md)
