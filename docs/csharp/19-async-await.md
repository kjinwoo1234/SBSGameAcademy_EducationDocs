# Chapter 19 async/await

## 학습 목표
- 동기와 비동기의 차이를 구분한다.
- `async`/`await`와 `Task.Delay`로 비동기 메서드를 작성할 수 있다.
- `async Task`와 `async void`의 쓰임 차이를 구분한다.

## 본문

### 19-1 비동기 개념

**동기**는 한 일이 끝날 때까지 다음 줄로 못 가는 흐름입니다. **비동기**는 오래 걸리는 일을 시작하는 동안 프로그램이 다른 일을 이어 갈 수 있게 하는 흐름입니다. 콘솔에서는 네트워크 대신 **`Task.Delay`**로 “잠시 기다리기”를 연습합니다. `Task.Delay`는 지정한 밀리초만큼 기다린 뒤 끝나는 비동기 작업입니다.

| 구분 | 특징 | 이럴 때 |
|---|---|---|
| 동기 | 끝날 때까지 현재 흐름이 멈춘 느낌 | 짧은 계산 |
| 비동기 | 대기하는 동안에도 다른 작업을 이어 갈 수 있음 | 지연·파일·원격 호출 |

### 19-2 `async`와 `await`

비동기 메서드는 보통 **`Task`** 또는 **`Task<T>`**를 반환합니다. **`Task`**는 “나중에 끝나는 일”을 나타내는 타입입니다. 메서드 선언에 **`async`**를 붙이고, 본문에서 **`await`**로 “이 작업이 끝날 때까지 여기서 이어간다”고 표시합니다. `await` 오른쪽에는 `Task.Delay(500)`처럼 기다릴 작업을 둡니다. `500`은 약 0.5초입니다.

입문용 프로그램 진입점은 `static async Task Main()` 형태로 둡니다. 파일 위에는 `using System.Threading.Tasks;`가 필요합니다.

아래를 실행해 `start` 다음에 잠시 쉬고 `done`이 나오는지 확인해 보세요.

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
- `await Task.Delay(500);`은 약 0.5초 기다린 뒤 다음 줄로 갑니다.
- 두 `WriteLine`은 그 순서대로 출력됩니다.

### 19-3 예외 처리와 `async Task` / `async void`

`async` 메서드 안에서 난 예외는 **`await`하는 쪽**의 `try-catch`로 받는 경우가 많습니다. 일반 메서드는 **`async Task`** 또는 **`async Task<T>`**를 반환하게 두고, 호출부에서 `await`합니다.

**`async void`**는 호출부가 `await`할 수 없어 예외를 잡기 어렵습니다. 입문에서는 이벤트처럼 반환 타입을 바꿀 수 없는 특수 자리만 제외하고 **`async Task`**를 씁니다. **이벤트**는 어떤 일이 일어났을 때 등록해 둔 메서드를 호출하는 알림 연결이며, 자세한 문법은 뒤 장에서 다룹니다.

| 반환 | 권장 | 이유 |
|---|---|---|
| `async Task` / `Task<T>` | 일반 메서드 | `await`·예외 전파 가능 |
| `async void` | 특수 진입점만 | 호출부가 `await` 불가 |

| 용어 | 의미 |
|---|---|
| Task | 나중에 끝나는 비동기 작업을 나타내는 타입 |
| async void | 반환이 없어 호출부가 완료·예외를 기다리기 어려운 형태 |

아래를 실행해 `오류 발생`이 출력되는지 확인해 보세요.

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        try
        {
            await RiskyAsync();
        }
        catch (Exception)
        {
            Console.WriteLine("오류 발생");
        }
    }

    static async Task RiskyAsync()
    {
        await Task.Delay(100);
        throw new Exception("연습용 오류");
    }
}
```

**예상 출력**

```text
오류 발생
```

**코드 해석**
- `RiskyAsync`는 `async Task`를 반환하고, 안에서 예외를 던집니다.
- `Main`이 `await RiskyAsync()`를 `try` 안에서 호출하므로 `catch`에서 메시지를 출력합니다.
- 반환을 `async void`로 바꾸면 `Main`에서 `await`할 수 없어 같은 방식으로 잡기 어렵습니다.

이 장에서는 `async`/`await` 기본과 `Task.Delay`·예외 경로까지입니다. 취소·동시 실행 같은 심화는 뒤 장으로 미룹니다.

### 연습문제

**문제 1**
- 문제: 1초 후 메시지를 출력하는 비동기 프로그램을 작성하세요.
- 입력: 없음
- 출력:
  ```text
  start
  done
  ```
- 조건: `async Task Main`, `await Task.Delay(1000)`, `start`/`done` 순서

**문제 2**
- 문제: 비동기 메서드에서 던진 예외를 `await` 호출부의 `try-catch`로 처리하세요.
- 입력: 없음
- 출력:
  ```text
  오류 발생
  ```
- 조건: `async Task` 메서드 하나에서 `throw`, `Main`에서 `await`를 `try-catch`로 감싸기

**문제 3**
- 문제: 다음 중 일반 메서드에 권장되는 형태를 고르고, 이유를 한 줄로 쓰세요.
- 입력: 없음
- 출력: `async Task` 와 “`await`로 완료·예외를 다룰 수 있다”는 취지의 한 줄
- 조건: 선택지 `async Task` / `async void` 중 하나. 본문 표 기준

### 정답 포인트

- 문제 1: `Console.WriteLine("start");` → `await Task.Delay(1000);` → `Console.WriteLine("done");`
- 문제 2: `try { await RiskyAsync(); } catch { Console.WriteLine("오류 발생"); }`
- 문제 3: `async Task` 권장. `async void`는 호출부가 `await`하지 못함
- 공통: 대기는 `Task.Delay`+`await`, 예외는 `await` 경로의 `try-catch`

---

[상위 문서로 돌아가기](./README.md)
