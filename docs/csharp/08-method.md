# Chapter 08 메서드

## 학습 목표
- 메서드 선언·호출·반환 구조를 이해한다.
- 매개변수 전달 방식인 값 전달, `ref`, `out`을 구분한다.
- 같은 이름·다른 시그니처의 **오버로딩**을 구분해 호출한다.

## 본문

### 08-1 메서드 기본

**메서드**는 반복되는 코드를 이름 붙여 묶어 두고, 필요할 때 **호출**해 재사용하는 단위입니다. 형태는 `반환형 이름(매개변수)`입니다. 예로 `static int Add(int a, int b)`는 정수 두 개를 받아 정수를 돌려줍니다. 반환형이 **`void`**이면 값을 반환하지 않고, 안에서 `Console.WriteLine`처럼 일만 합니다.

호출은 `Add(3, 4)`처럼 이름 뒤에 괄호를 붙입니다. 괄호 안 값을 **인자**, 선언부 칸을 **매개변수**라고 부릅니다. 메서드는 `Main`과 같은 `class` 안에 두되, `Main` 밖·다른 메서드와 나란히 적습니다.

아래를 실행해 `Add`의 반환값과 `Print`의 출력만 하는 `void` 차이를 확인해 보세요.

```csharp
using System;

class Program
{
    static int Add(int a, int b)
    {
        return a + b;
    }

    static void Print(string message)
    {
        Console.WriteLine(message);
    }

    static void Main()
    {
        int sum = Add(3, 4);
        Console.WriteLine(sum);
        Print("호출 완료");
    }
}
```

**예상 출력**

```text
7
호출 완료
```

**코드 해석**
- `Add`는 `return`으로 합을 돌려주고, `Main`이 그 값을 `sum`에 담아 출력합니다.
- `Print`는 `void`라 반환값이 없고, 안에서 바로 출력합니다.
- `Add`와 `Print`는 `Main`과 같은 `class` 안, `Main` 밖에 있습니다.

### 08-2 매개변수 전달

기본은 **값 전달**입니다. 메서드에 넘긴 복사본이 바뀌어도 호출쪽 변수는 그대로입니다. 호출쪽 변수를 메서드가 직접 바꾸려면 **`ref`** 또는 **`out`**을 씁니다. `ref`는 호출 전에 변수를 초기화해야 하고, `out`은 메서드 안에서 반드시 대입해야 합니다. 선언과 호출 **양쪽**에 `ref`/`out`을 적어야 합니다.

| 방식 | 의미 | 이럴 때 | 주의점 |
|---|---|---|---|
| 값 전달 | 복사본 전달 | 원본을 바꾸지 않을 때 | 메서드 안 대입이 원본에 안 보임 |
| `ref` | 같은 변수 공유 | 기존 값을 읽어 바꾼 뒤 되돌릴 때 | 호출 전 변수 초기화 필요 |
| `out` | 결과 칸으로 사용 | 메서드가 값을 채워 돌려줄 때 | 메서드 안에서 대입 필수 |

먼저 값 전달입니다. 아래를 실행해 `Main`의 `x`가 그대로인지 확인해 보세요.

```csharp
using System;

class Program
{
    static void TryDouble(int n)
    {
        n = n * 2;
    }

    static void Main()
    {
        int x = 5;
        TryDouble(x);
        Console.WriteLine(x);
    }
}
```

**예상 출력**

```text
5
```

**코드 해석**
- `TryDouble` 안의 `n`은 복사본이라 `10`이 되어도 `Main`의 `x`는 `5`입니다.

이어서 `ref`입니다. 선언·호출 양쪽에 `ref`를 붙입니다.

```csharp
using System;

class Program
{
    static void DoubleValue(ref int n)
    {
        n = n * 2;
    }

    static void Main()
    {
        int x = 5;
        DoubleValue(ref x);
        Console.WriteLine(x);
    }
}
```

**예상 출력**

```text
10
```

**코드 해석**
- `DoubleValue(ref x)`는 `x`와 같은 변수를 공유하므로 `5`가 `10`으로 바뀝니다.
- 호출 전 `x`를 초기화해 두었습니다.

마지막으로 `out`입니다. 메서드가 칸을 채워 주고, 호출쪽은 미리 값을 넣지 않아도 됩니다.

```csharp
using System;

class Program
{
    static void Divide(int n, out int quotient, out int remainder)
    {
        quotient = n / 2;
        remainder = n % 2;
    }

    static void Main()
    {
        int q;
        int r;
        Divide(7, out q, out r);
        Console.WriteLine(q);
        Console.WriteLine(r);
    }
}
```

**예상 출력**

```text
3
1
```

**코드 해석**
- `out q`, `out r`은 결과가 들어갈 칸입니다. `Divide` 안에서 반드시 대입합니다.
- `7 / 2`의 몫은 `3`, 나머지는 `1`입니다.

### 08-3 메서드 오버로딩

**오버로딩**은 같은 이름 메서드를 **시그니처**가 다르면 여러 개 둘 수 있는 기능입니다. 시그니처는 이름과 매개변수 타입·개수로 메서드를 구분하는 정보입니다. `Add(int, int)`와 `Add(double, double)`처럼 인자 타입에 맞는 버전이 선택됩니다. **반환형만** 다르게 해서 오버로딩할 수는 없습니다.

| 용어 | 의미 |
|---|---|
| 메서드 시그니처 | 메서드를 구분하는 정보. 이름, 매개변수 타입·개수 |
| 오버로딩 | 같은 이름 메서드를 시그니처로 구분해 여러 개 정의하는 기능 |

아래를 실행해 인자 타입에 따라 어느 `Add`가 선택되는지 확인해 보세요. `ref`·`out`은 앞 소절에서 이미 다뤘습니다.

```csharp
using System;

class Program
{
    static int Add(int a, int b)
    {
        return a + b;
    }

    static double Add(double a, double b)
    {
        return a + b;
    }

    static void Main()
    {
        Console.WriteLine(Add(3, 4));
        Console.WriteLine(Add(1.5, 2.5));
    }
}
```

**예상 출력**

```text
7
4
```

**코드 해석**
- `Add(3, 4)`는 `int` 버전이 선택됩니다.
- `Add(1.5, 2.5)`는 `double` 버전이 선택됩니다.

### 연습문제

**문제 1**
- 문제: `Print` 메서드를 문자열 버전과 정수 버전으로 오버로딩하세요.
- 입력: 없음. 코드에서 `Print("hi");`와 `Print(7);`을 호출합니다.
- 출력:
  ```text
  hi
  7
  ```
- 조건: 메서드 이름 `Print` 동일, 매개변수 타입만 다름

**문제 2**
- 문제: `ref`로 전달한 정수를 2배로 바꾸는 메서드를 작성하세요.
- 입력: 없음. 예로 변수 `n = 6` 후 메서드를 호출합니다.
- 출력: `12`
- 조건: 선언·호출 모두 `ref` 사용

**문제 3**
- 문제: 정수 하나를 받아 몫과 나머지를 `out`으로 채우는 메서드를 작성하세요. 나눗셈은 2로 합니다.
- 입력: 없음. 예로 `Divide(9, out q, out r);` 후 `q`와 `r`을 출력합니다.
- 출력:
  ```text
  4
  1
  ```
- 조건: 선언·호출 모두 `out` 사용. 메서드 안에서 `out` 매개변수에 대입

### 정답 포인트

- 문제 1: `static void Print(string s)`, `static void Print(int n)` — 시그니처로 구분
- 문제 2: `void Scale(ref int n) { n *= 2; }` 후 `Scale(ref n);`
- 문제 3: `void Divide(int n, out int q, out int r) { q = n / 2; r = n % 2; }` 후 `Divide(9, out q, out r);`
- 공통: 원본 변경이 필요하면 `ref`/`out`, 아니면 값 전달

---

[상위 문서로 돌아가기](./README.md)
