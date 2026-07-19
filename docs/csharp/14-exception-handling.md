# Chapter 14 예외 처리

## 학습 목표
- `try`·`catch`·`finally` 실행 순서를 구분한다.
- 예외 메시지로 오류를 알리고 복구할 수 있다.
- 규칙 위반 시 `throw`로 호출 쪽에 오류를 알릴 수 있다.

## 본문

### 14-1 예외 기본

**예외**는 프로그램 실행 중 생긴 오류 상황입니다. 잡지 않으면 호출한 메서드 쪽으로 오류가 올라갑니다. 이렇게 위로 전달되는 동작을 **전파**라고 합니다. 전파가 막히지 않으면 프로그램이 비정상 종료될 수 있습니다. 앞 장에서 배운 `int.Parse("abc")`처럼 숫자로 읽을 수 없는 글자를 넣으면 **`FormatException`**이 발생합니다.

| 용어 | 의미 |
|---|---|
| 예외 | 실행 중 발생한 오류 상황 |
| 전파 | 처리되지 않은 예외가 호출한 쪽으로 올라가는 동작 |

### 14-2 `try-catch-finally`

**`try`** 안에는 실패할 수 있는 코드를 둡니다. 문제가 나면 **`catch`**에서 잡아 사용자에게 메시지를 보이거나 기본값으로 이어가는 **복구**를 합니다. **`finally`**는 예외가 나든 안 나든 **마지막에** 실행되어 로그 한 줄 같은 정리에 씁니다. 파일 닫기처럼 자원 정리는 다음 장 파일 입출력에서 이어서 다룹니다. 이 장에서는 try/catch/finally 흐름만 익힙니다.

| 구문 | 역할 | 이럴 때 |
|---|---|---|
| `try` | 실행 시도 | 실패 가능한 코드 |
| `catch` | 예외 처리 | 메시지 출력·복구 |
| `finally` | 항상 정리 | 예외 여부와 무관히 할 일 |

아래를 `Main`에 넣고 실행해, `"abc"` 파싱이 예외로 잡히고 `finally` 메시지가 항상 나오는지 확인해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        try
        {
            int n = int.Parse("abc");
            Console.WriteLine(n);
        }
        catch (FormatException)
        {
            Console.WriteLine("입력 형식 오류");
        }
        finally
        {
            Console.WriteLine("정리 완료");
        }
    }
}
```

**예상 출력**

```text
입력 형식 오류
정리 완료
```

**코드 해석**
- `"abc"`를 정수로 바꾸려다 `FormatException`이 납니다.
- `catch`가 잡아 프로그램을 끝내지 않고 메시지를 출력합니다.
- `finally`는 catch 이후에도 실행되어 `정리 완료`가 나옵니다.

### 14-3 사용자 정의 검사와 `throw`

규칙 위반을 발견하면 **`throw new Exception("메시지");`**로 호출 쪽에 알릴 수 있습니다. `throw`는 예외를 **새로 발생**시키는 구문입니다. 점수가 0~100 밖인데도 조용히 잘못된 값을 유지하는 대신, 문제임을 명시할 때 씁니다. 더 구체적인 예외 타입도 있지만, 입문에서는 `Exception`과 메시지 문자열이면 충분합니다.

아래를 실행해 범위 밖 점수에서 `throw`가 나고, `catch`가 메시지를 출력하는지 확인해 보세요.

```csharp
using System;

class Program
{
    static void CheckScore(int score)
    {
        if (score < 0 || score > 100)
        {
            throw new Exception("점수 범위 오류");
        }
        Console.WriteLine(score);
    }

    static void Main()
    {
        try
        {
            CheckScore(120);
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
}
```

**예상 출력**

```text
점수 범위 오류
```

**코드 해석**
- `CheckScore(120)`에서 범위 밖이라 `throw`가 실행됩니다.
- `Main`의 `catch`가 예외를 받아 `ex.Message`를 출력합니다.
- 정상 점수면 `throw` 없이 `Console.WriteLine(score)`만 실행됩니다.

### 연습문제

**문제 1**
- 문제: 나눗셈에서 제수가 0이면 예외를 처리하세요.
- 입력: 없음. 코드에서 `a = 10`, `b = 0`으로 두고 시작해도 됩니다.
- 출력: `0으로 나눌 수 없습니다`
- 조건: `try-catch`로 `DivideByZeroException`을 잡거나, 나누기 전 검사 후 같은 메시지 출력

**문제 2**
- 문제: 점수가 0~100 밖이면 예외를 던지세요.
- 입력: 없음. 예 점수 `120`
- 출력: `점수 범위 오류`
- 조건: 범위 밖일 때 `throw`, 호출부에서 `catch`

**문제 3**
- 문제: `try`에서 예외가 나도 `finally`의 문구가 출력되게 하세요.
- 입력: 없음. `int.Parse("x")`처럼 실패하는 코드를 `try`에 두어도 됩니다.
- 출력:
  ```text
  처리 실패
  종료 정리
  ```
- 조건: `catch`에서 `처리 실패`, `finally`에서 `종료 정리` 출력

### 정답 포인트

- 문제 1: `try { Console.WriteLine(a / b); } catch (DivideByZeroException) { Console.WriteLine("0으로 나눌 수 없습니다"); }`
- 문제 2: `if (score < 0 || score > 100) throw new Exception("점수 범위 오류");` + `try-catch`
- 문제 3: `catch`와 `finally`를 모두 두고, 예외가 나도 `finally`가 실행되는지 확인
- 공통: 예외는 문제를 숨기는 도구가 아니라, 문제를 명확히 알리는 도구입니다.

---

[상위 문서로 돌아가기](./README.md)
