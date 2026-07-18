# Chapter 21 delegate

## 학습 목표
- 델리게이트의 개념과 사용 목적을 이해한다.
- 메서드를 변수처럼 전달/저장하는 방식을 익힌다.
- 멀티캐스트(`+=`)와 `Action`/`Func`를 구분해 쓴다.

## 본문

### 21-1 delegate 기본

**델리게이트(delegate)**는 **메서드를 가리키는 타입**입니다. `delegate void LogHandler(string msg);`처럼 **시그니처**(매개변수·반환형)를 정해 두면, 같은 모양의 메서드를 변수에 담아 호출·교체할 수 있습니다.

### 21-2 다중 호출

하나의 델리게이트에 **`+=`**로 여러 메서드를 이으면 **멀티캐스트**로 순차 호출됩니다. 빼려면 **`-=`**입니다.

### 21-3 `Action`/`Func`

직접 `delegate` 타입을 쓰기 전에 내장 타입을 쓰는 경우가 많습니다. **`Action`**은 반환 없음, **`Func`**는 반환 있음입니다.

| 타입 | 반환 | 예 |
|---|---|---|
| `Action<string>` | 없음 | 로그 출력 |
| `Func<int, int, int>` | 있음 | 두 수 연산 |
| 커스텀 `delegate` | 원하는 형태 | 도메인 이름 붙일 때 |

| 용어 | 의미 |
|---|---|
| 시그니처(signature) | 메서드를 구분하는 정보(매개변수/반환형) |
| 멀티캐스트 | 한 델리게이트에 여러 메서드를 연결해 순차 실행하는 방식 |

아래 코드를 실행해 `delegate test`와 멀티캐스트 두 줄 로그를 확인해 보세요.

```csharp
using System;

delegate void LogHandler(string msg);

class Program
{
    static void ConsoleLog(string msg)
    {
        Console.WriteLine(msg);
    }

    static void ConsoleLog2(string msg)
    {
        Console.WriteLine("[2] " + msg);
    }

    static void Main()
    {
        LogHandler handler = ConsoleLog;
        handler("delegate test");

        handler += ConsoleLog2;
        handler("multi");
    }
}
```

**예상 출력**

```text
delegate test
multi
[2] multi
```

**코드 해석**
- `LogHandler`는 `string`→`void` 메서드만 담습니다.
- `handler = ConsoleLog` 후 `handler("delegate test")`로 호출합니다.
- `+= ConsoleLog2` 뒤 `"multi"`는 두 메서드가 순서대로 실행됩니다.

### 연습문제

**문제 1**
- 문제: `Func<int, int, int>`로 덧셈/곱셈을 바꿔 실행하세요.
- 입력: 없음(예: `3`, `4`)
- 출력: 덧셈 시 `7`, 곱셈으로 바꾼 뒤 `12`
- 조건: 같은 `Func` 변수에 다른 메서드(또는 람다) 재할당

**문제 2**
- 문제: 로그 메서드 2개를 `+=`로 연결해 한 번 호출하세요.
- 입력: 없음(메시지 `"hi"`)
- 출력: 로그 2줄
- 조건: 멀티캐스트 `+=`

### 정답 포인트

- 문제 1: `Func<int, int, int> op = (a, b) => a + b;` 후 `op = (a, b) => a * b;`
- 문제 2: `handler += MethodA; handler += MethodB; handler(msg);`
- 공통: 시그니처가 맞아야 연결. 전략 교체·콜백에 유용

---

[상위 문서로 돌아가기](./README.md)
