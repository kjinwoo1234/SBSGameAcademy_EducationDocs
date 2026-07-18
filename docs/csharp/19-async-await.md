# Chapter 19 async/await

## 학습 목표
- 비동기 프로그래밍의 필요성과 기본 흐름을 이해한다.
- `async`/`await`를 이용해 비동기 메서드를 작성할 수 있다.
- `async Task`와 `async void`의 쓰임 차이를 구분한다.

## 본문

### 19-1 비동기 개념

**동기**는 한 일이 끝날 때까지 다음 줄로 못 가는 흐름입니다. **비동기**는 오래 걸리는 일(대기·네트워크·파일 등)을 시작하는 동안 **다른 일**을 이어 갈 수 있게 합니다. 콘솔에서도 `Task.Delay`로 “기다림”을 연습할 수 있습니다.

| 구분 | 특징 | 이럴 때 |
|---|---|---|
| 동기 | 끝날 때까지 현재 흐름이 멈춘 느낌 | 짧은 계산 |
| 비동기 | 대기 중에도 모델상 다른 작업 진행 가능 | I/O·지연·원격 호출 |

### 19-2 `async`와 `await`

비동기 메서드는 보통 **`Task`** 또는 **`Task<T>`**를 반환합니다. 메서드에 **`async`**를 붙이고, 안에서 **`await`**로 “이 작업 끝날 때까지 여기서 이어감”을 표시합니다. `await`는 완료를 기다리되, 동기 `Thread.Sleep`처럼 스레드를 붙잡아 두는 패턴을 줄이는 데 쓰입니다. (`async Task Main()`은 입문용 진입점 형태입니다. 지금은 패턴만 따라 하면 됩니다.)

### 19-3 예외 처리와 주의점

`async` 메서드 안 예외는 **`await`하는 쪽**의 `try-catch`로 받는 경우가 많습니다. **`async void`**는 예외를 호출부가 잡기 어렵고, 보통 **이벤트 핸들러** 외에는 `async Task`를 권장합니다.

| 반환 | 권장 | 이유 |
|---|---|---|
| `async Task` / `Task<T>` | 일반 메서드 | await·예외 전파 가능 |
| `async void` | 이벤트 등 특수 | 호출부가 await 불가 |

| 용어 | 의미 |
|---|---|
| Task | 비동기 작업 단위를 나타내는 타입 |
| 블로킹(blocking) | 현재 스레드가 작업 완료까지 멈춰 있는 상태 |

아래 코드를 실행해 `start` 후 잠시 뒤 `done`이 나오는지 확인해 보세요.

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        Console.WriteLine("start");
        await Task.Delay(500);
        Console.WriteLine("done");
    }
}
```

**예상 출력**

```text
start
done
```

**코드 해석**
- `async Task Main()`이 비동기 진입점입니다.
- `await Task.Delay(500)`이 약 0.5초 지연 후 다음 줄로 진행합니다.
- 두 출력이 순서대로 나타납니다.

### 연습문제

**문제 1**
- 문제: 1초 후 메시지를 출력하는 비동기 메서드를 작성하세요.
- 입력: 없음
- 출력:
  ```text
  start
  done
  ```
  (`Task.Delay(1000)` 사용)
- 조건: `async`/`await` + `Task.Delay`

**문제 2**
- 문제: 비동기 메서드 내부 예외를 `try-catch`로 처리하세요.
- 입력: 없음
- 출력: `오류 발생` (또는 유사 메시지)
- 조건: `await` 호출부를 `try-catch`로 감싸기 (`throw`한 Task 등)

### 정답 포인트

- 문제 1: `await Task.Delay(1000);` 전후 로그
- 문제 2: `try { await RiskyAsync(); } catch { Console.WriteLine("오류 발생"); }`
- 공통: I/O·대기에 async, 예외도 await 경로에서 처리. `async void` 남용 금지

---

[상위 문서로 돌아가기](./README.md)
