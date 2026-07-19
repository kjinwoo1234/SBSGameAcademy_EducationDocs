# Chapter 27 비동기 심화

## 학습 목표
- 여러 `Task`를 `Task.WhenAll`로 함께 기다리고 결과를 받을 수 있다.
- `CancellationTokenSource`와 `CancellationToken`으로 작업을 취소할 수 있다.
- 취소와 일반 실패를 구분해 처리할 수 있다.

## 본문

### 27-1 다중 비동기 작업 조합

**`Task.WhenAll`**은 여러 `Task`를 **함께 기다립니다**. 모두 끝나면 결과 배열을 받을 수 있습니다. 하나라도 실패하거나 취소되면 예외가 올라오므로, 호출부는 `try-catch`로 감싸는 편이 안전합니다. 19장에서 배운 `async`/`await`와 `Task.Delay` 위에, “여러 작업을 한꺼번에 기다리기”를 붙인 형태입니다.

아래를 실행해 세 작업이 모두 끝난 뒤 합계가 출력되는지 확인해 보세요. 지연은 짧게 두었습니다.

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task<int> WorkAsync(int value, int delayMs)
    {
        await Task.Delay(delayMs);
        return value * 2;
    }

    static async Task Main()
    {
        Task<int> t1 = WorkAsync(10, 100);
        Task<int> t2 = WorkAsync(20, 150);
        Task<int> t3 = WorkAsync(30, 200);

        int[] results = await Task.WhenAll(t1, t2, t3);
        Console.WriteLine(results[0] + results[1] + results[2]);
    }
}
```

**예상 출력**

```text
120
```

**코드 해석**
- `WorkAsync` 세 개를 시작한 뒤, 각각을 따로 `await`하지 않고 `Task.WhenAll`로 한꺼번에 기다립니다.
- 결과는 `20`, `40`, `60`이고 합은 `120`입니다.
- 세 Delay를 순서대로만 기다리면 더 오래 걸리지만, WhenAll은 겹쳐 기다리므로 총 대기 시간은 그중 긴 쪽에 가깝습니다.

### 27-2 취소 토큰

**`CancellationTokenSource`**는 취소 신호를 만드는 객체입니다. **`CancellationToken`**은 그 신호를 작업에 전달하는 값입니다. `cts.Token`을 `Task.Delay(delayMs, token)`처럼 넘기고, 필요하면 `token.ThrowIfCancellationRequested()`로 “지금 취소되었으면 예외”를 냅니다. `CancelAfter(밀리초)`는 일정 시간 뒤 자동으로 취소 신호를 보냅니다. 토큰을 작업에 안 넘기면 취소가 무효입니다.

`CancellationTokenSource`도 사용이 끝나면 정리하는 편이 좋습니다. 20장처럼 `using (CancellationTokenSource cts = new CancellationTokenSource())` 형태를 씁니다.

아래를 실행해 `CancelAfter(500)` 때문에 취소 메시지가 나오는지 확인해 보세요.

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
        using (CancellationTokenSource cts = new CancellationTokenSource())
        {
            cts.CancelAfter(500);

            try
            {
                Task<int> t1 = WorkAsync(10, 200, cts.Token);
                Task<int> t2 = WorkAsync(20, 400, cts.Token);
                Task<int> t3 = WorkAsync(30, 800, cts.Token);

                int[] results = await Task.WhenAll(t1, t2, t3);
                Console.WriteLine(results[0] + results[1] + results[2]);
            }
            catch (OperationCanceledException)
            {
                Console.WriteLine("작업이 취소되었습니다.");
            }
        }
    }
}
```

**예상 출력**

```text
작업이 취소되었습니다.
```

**코드 해석**
- `t3`는 800ms가 필요하지만, 500ms에 취소 신호가 갑니다.
- `WhenAll` 대기 중 `OperationCanceledException`이 나고 `catch`에서 메시지를 출력합니다.
- 토큰을 `Delay`와 `ThrowIfCancellationRequested`에 넘긴 것이 핵심입니다.

### 27-3 실수 포인트와 방어 패턴

토큰을 만들어 놓고 전달을 잊는 실수가 흔합니다. 또한 작업을 무작정 많이 병렬로 열면 오히려 느려질 수 있습니다. 입문에서는 **소수 Task + WhenAll + 취소·예외 처리**부터 익힙니다. 취소는 `OperationCanceledException`, 그 외 오류는 일반 예외로 구분해 메시지를 나누면 원인을 찾기 쉽습니다.

| 항목 | 목적 | 장점 | 주의점 |
|---|---|---|---|
| `Task.WhenAll` | 여러 작업 동시 대기 | 총 대기 시간 단축 | 일부 실패·취소 시 예외 처리 필요 |
| `CancellationToken` | 작업 취소 제어 | 타임아웃·사용자 취소 대응 | 토큰 전달 누락 시 무효 |
| `Task.Delay` + 토큰 | 대기 중 취소 | 반응성 향상 | 취소 예외 흐름을 점검 |

이 장에서는 WhenAll과 취소 토큰까지입니다. 고급 스케줄러·채널 같은 심화는 다루지 않습니다.

### 연습문제

**문제 1**
- 문제: 작업 3개를 병렬로 실행하고 결과 합계를 출력하세요.
- 입력: 없음. 예: 값 `1`,`2`,`3`을 각각 두 배로 만들고 Delay는 100ms 전후
- 출력: `12`
- 조건: `Task.WhenAll` 사용. 취소 없이 완료

**문제 2**
- 문제: 진행 중 작업을 취소하세요.
- 입력: 없음. `CancelAfter(300)` 사용
- 출력: `취소됨`
- 조건: `CancellationTokenSource` + 토큰을 `Task.Delay`에 전달

**문제 3**
- 문제: 실패와 취소를 **따로** 실행해 구분해 처리하세요. `WhenAll`로 한곳에 묶지 않아도 됩니다.
- 입력: 없음
  - 실패용: `await` 전에 `throw new Exception("fail");` 하는 메서드
  - 취소용: `CancelAfter(200)` + 긴 `Task.Delay(..., token)`
- 출력:
  ```text
  실패
  취소
  ```
- 조건: 실패는 `catch (Exception)`, 취소는 `catch (OperationCanceledException)`처럼 분기를 나눌 것

### 정답 포인트

- 문제 1: 여러 `WorkAsync` 시작 후 `await Task.WhenAll(...)`로 합계 출력
- 문제 2: `cts.Token`을 Delay에 전달하고 `CancelAfter(300)` 후 취소 메시지 출력
- 문제 3: 실패 호출과 취소 호출을 각각 `try-catch`로 감싸 `실패`/`취소` 출력
- 공통: 동시 실행보다 실패·취소 제어가 심화의 핵심

---

[상위 문서로 돌아가기](./README.md)
