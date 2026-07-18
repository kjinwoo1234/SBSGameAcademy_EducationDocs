# Chapter 14 예외 처리

## 학습 목표
- `try-catch-finally` 흐름을 이해한다.
- 예외 메시지와 오류 복구 전략의 기초를 익힌다.
- 필요 시 `throw`로 오류를 명확히 알릴 수 있다.

## 본문

### 14-1 예외 기본

**예외(exception)**는 실행 중 생긴 오류를 프로그램이 **구조적으로 알리고 처리**하는 방법입니다. 잡지 않으면 호출 스택 위로 **전파**되어 프로그램이 비정상 종료될 수 있습니다. `int.Parse("abc")`처럼 형식이 맞지 않으면 `FormatException`이 발생합니다.

### 14-2 `try-catch-finally`

**`try`** 안에서 위험할 수 있는 코드를 실행하고, 문제가 나면 **`catch`**에서 처리합니다. **`finally`**는 예외가 나든 안 나든 **마지막에** 실행되어 정리(로그 한 줄 등)에 씁니다. (파일 닫기 등은 나중에 `using`과도 연결됩니다. 이 장에서는 try/catch/finally 흐름만 익힙니다.)

| 구문 | 역할 | 이럴 때 |
|---|---|---|
| `try` | 실행 시도 | 실패 가능한 코드 |
| `catch` | 예외 처리 | 사용자 메시지·복구 |
| `finally` | 항상 정리 | 예외 여부와 무관히 할 일 |

### 14-3 사용자 정의 검사와 `throw`

규칙 위반(점수 범위 밖 등)을 발견하면 **`throw new Exception("...")`**(또는 더 구체적인 예외 타입)로 호출 쪽에 알릴 수 있습니다. “조용히 잘못된 값 유지”보다 “문제임을 명시”하는 도구입니다.

| 용어 | 의미 |
|---|---|
| 예외 전파 | 처리되지 않은 예외가 호출 스택 상위로 전달되는 동작 |
| `finally` | 예외 발생 여부와 관계없이 마지막에 실행되는 정리 구문 |

아래 코드를 실행해 `"abc"` 파싱이 예외로 잡히고, `finally` 메시지가 항상 나오는지 확인해 보세요.

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

### 연습문제

**문제 1**
- 문제: 나눗셈에서 제수가 0이면 예외를 처리하세요.
- 입력: 없음(코드에서 `10 / 0` 시도 또는 `a=10, b=0`)
- 출력: `0으로 나눌 수 없습니다` (문구 유사하면 됨)
- 조건: `try-catch`로 `DivideByZeroException` 또는 나누기 전 검사 후 메시지

**문제 2**
- 문제: 점수가 0~100 밖이면 예외를 던지세요.
- 입력: 없음(예: 점수 `120`)
- 출력: `점수 범위 오류` (또는 catch한 메시지)
- 조건: 범위 밖일 때 `throw`, 호출부에서 `catch`

### 정답 포인트

- 문제 1: `try { Console.WriteLine(a / b); } catch (DivideByZeroException) { ... }`
- 문제 2: `if (score < 0 || score > 100) throw new Exception("점수 범위 오류");` + `try-catch`
- 공통: 예외는 문제 숨기기가 아니라 문제 명확화. 사용자에게 읽을 수 있는 메시지

---

[상위 문서로 돌아가기](./README.md)
