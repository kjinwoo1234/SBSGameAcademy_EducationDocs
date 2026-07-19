# Chapter 21 delegate

## 학습 목표
- 커스텀 `delegate` 타입에 메서드를 담아 호출할 수 있다.
- `+=`로 여러 메서드를 이어 멀티캐스트 호출할 수 있다.
- `Action`/`Func`와 커스텀 `delegate`의 쓰임을 구분한다.

## 본문

### 21-1 delegate 기본

**델리게이트**는 **메서드를 가리키는 타입**입니다. `delegate void LogHandler(string msg);`처럼 **시그니처**를 정해 둡니다. 시그니처는 매개변수 타입·개수와 반환형을 말합니다. 같은 모양의 메서드를 변수에 담아 호출하거나 다른 메서드로 바꿀 수 있습니다.

아래를 실행해 `delegate test`가 한 줄 출력되는지 확인해 보세요.

```csharp
using System;

delegate void LogHandler(string msg);

class Program
{
    static void ConsoleLog(string msg)
    {
        Console.WriteLine(msg);
    }

    static void Main()
    {
        LogHandler handler = ConsoleLog;
        handler("delegate test");
    }
}
```

**예상 출력**

```text
delegate test
```

**코드 해석**
- `LogHandler`는 `string`을 받고 반환이 없는 메서드만 담습니다.
- `handler = ConsoleLog`로 메서드를 변수에 넣습니다.
- `handler("delegate test")`는 `ConsoleLog`를 호출하는 것과 같습니다.

### 21-2 다중 호출

하나의 델리게이트에 **`+=`**로 메서드를 더 이으면 **멀티캐스트**가 됩니다. 한 번 호출하면 연결된 메서드가 **등록 순서대로** 실행됩니다. 빼려면 **`-=`**를 씁니다.

아래를 실행해 `multi`와 `[2] multi`가 순서대로 나오는지 확인해 보세요.

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
        handler += ConsoleLog2;
        handler("multi");
    }
}
```

**예상 출력**

```text
multi
[2] multi
```

**코드 해석**
- `handler = ConsoleLog` 뒤에 `+= ConsoleLog2`로 두 번째 메서드를 잇습니다.
- `handler("multi")`는 두 메서드를 순서대로 호출합니다.
- 첫 줄은 `ConsoleLog`, 둘째 줄은 `ConsoleLog2` 결과입니다.

### 21-3 `Action`/`Func`

매번 `delegate` 키워드로 타입을 만들기보다 내장 타입을 쓰는 경우가 많습니다. **`Action`**은 반환이 없고, **`Func`**는 반환이 있습니다. 17장 람다에서 쓴 것과 같은 계열이며, 여기서는 **메서드 이름을 담아** 바꿔 쓰는 점에 초점을 둡니다. 도메인에 이름을 붙이고 싶으면 커스텀 `delegate`를 씁니다.

| 타입 | 반환 | 이럴 때 |
|---|---|---|
| `Action<string>` | 없음 | 로그·출력 |
| `Func<int, int, int>` | 있음 | 두 수 연산 |
| 커스텀 `delegate` | 원하는 형태 | 이름 있는 계약 |

| 용어 | 의미 |
|---|---|
| 시그니처 | 매개변수와 반환형으로 메서드 모양을 정한 정보 |
| 멀티캐스트 | 한 델리게이트에 여러 메서드를 연결해 순차 실행 |

아래를 실행해 덧셈 `7` 다음에 곱셈 `12`가 나오는지 확인해 보세요.

```csharp
using System;

class Program
{
    static int Add(int a, int b)
    {
        return a + b;
    }

    static int Mul(int a, int b)
    {
        return a * b;
    }

    static void Main()
    {
        Func<int, int, int> op = Add;
        Console.WriteLine(op(3, 4));

        op = Mul;
        Console.WriteLine(op(3, 4));

        Action<string> log = Console.WriteLine;
        log("done");
    }
}
```

**예상 출력**

```text
7
12
done
```

**코드 해석**
- `Func<int, int, int>`에 `Add`를 담아 `op(3, 4)`로 `7`을 출력합니다.
- 같은 변수에 `Mul`을 다시 담으면 `12`가 됩니다.
- `Action<string>`에 `Console.WriteLine`을 담아 문자열만 출력합니다.

이 장에서는 델리게이트에 메서드를 담고, `+=`로 이으며, `Action`/`Func`와 비교하는 것까지입니다. 이벤트 문법은 다음 장에서 이어갑니다.

### 연습문제

**문제 1**
- 문제: `Func<int, int, int>`로 덧셈을 실행한 뒤 곱셈으로 바꿔 다시 실행하세요.
- 입력: 없음. 인자는 `3`, `4` 고정
- 출력:
  ```text
  7
  12
  ```
- 조건: 같은 `Func` 변수에 메서드 또는 람다를 재할당

**문제 2**
- 문제: 로그 메서드 2개를 `+=`로 연결해 `"hi"`를 한 번 호출하세요.
- 입력: 없음
- 출력:
  ```text
  hi
  [2] hi
  ```
- 조건: 커스텀 `delegate` 또는 `Action<string>` + 멀티캐스트 `+=`

**문제 3**
- 문제: “반환값이 있는 두 수 연산”에 더 맞는 타입을 `Action` / `Func` / 커스텀 `delegate` 관점에서 고르고 이유를 한 줄로 쓰세요.
- 입력: 없음
- 출력: `Func` 와 “반환이 있다”는 취지의 한 줄. 커스텀 `delegate`를 써도 반환형만 맞으면 가능하다고 적어도 됨
- 조건: 본문 표 기준. 반환 없는 `Action`만 고르지 말 것

### 정답 포인트

- 문제 1: `op = Add` 또는 `(a, b) => a + b` 후 곱셈으로 재할당, `op(3, 4)` 두 번
- 문제 2: `handler += MethodA; handler += MethodB; handler("hi");` 형태로 두 줄 출력
- 문제 3: `Func`가 기본 선택. 반환 있는 커스텀 `delegate`도 가능. `Action`은 반환 없음
- 공통: 시그니처가 맞아야 대입·연결된다

---

[상위 문서로 돌아가기](./README.md)
