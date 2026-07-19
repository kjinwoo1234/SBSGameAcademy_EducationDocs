# Chapter 17 람다

## 학습 목표
- `(매개변수) => 식` 형태의 람다 문법을 사용할 수 있다.
- `Func`으로 결과 있는 동작을, `Action`으로 결과 없는 동작을 담을 수 있다.
- 배열 정렬·필터에 람다를 넘겨 짧게 작성할 수 있다.

## 본문

### 17-1 람다 기본

**람다**는 이름 없는 **짧은 함수 표현식**입니다. 형태는 `(매개변수) => 식` 또는 매개변수가 하나면 `x => 식`입니다. 정렬 기준·필터 조건처럼 “한 번 넘기고 끝낼” 동작에 메서드를 따로 만들지 않아도 됩니다.

아래를 `Main`에 넣고 실행해, 람다를 변수에 담아 호출한 결과가 나오는지 확인해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        Func<int, int> doubleValue = x => x * 2;
        Console.WriteLine(doubleValue(5));
    }
}
```

**예상 출력**

```text
10
```

**코드 해석**
- `x => x * 2`는 정수 하나를 받아 두 배로 만드는 람다입니다.
- `Func<int, int>`는 “인자 `int` 하나, 결과 `int`”인 동작을 담는 타입입니다.
- `doubleValue(5)`처럼 메서드처럼 호출합니다.

### 17-2 Func과 Action

동작을 변수·인자로 넘길 때 자주 쓰는 타입이 **`Func`**과 **`Action`**입니다. `Func`은 결과가 있고, `Action`은 결과가 없습니다. 둘 다 메서드를 가리키는 타입 계열입니다. 문법은 이후 델리게이트 장에서 더 다루고, 지금은 람다를 담는 상자로 쓰면 됩니다.

| 타입 | 반환 | 예 | 이럴 때 |
|---|---|---|---|
| `Func<int, int>` | 있음 | `x => x * 2` | 계산·변환 |
| `Action<int>` | 없음 | `x => Console.WriteLine(x)` | 출력·부수 효과 |

아래를 실행해 `Action`이 값을 출력만 하는지 확인해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        Action<int> print = x => Console.WriteLine(x);
        print(7);
    }
}
```

**예상 출력**

```text
7
```

**코드 해석**
- `Action<int>`는 인자 `int` 하나, 반환 없음입니다.
- `print(7)`은 `Console.WriteLine(7)`과 같이 동작합니다.
- 계산 결과가 필요하면 `Func`, 출력만 하면 `Action`입니다.

### 17-3 배열에 람다 넘기기

앞 장에서 배운 **배열**에 람다를 넘기면 정렬·필터를 짧게 적을 수 있습니다. **`Array.Sort(배열, 비교람다)`**는 두 원소를 비교하는 람다로 순서를 정합니다. **`Array.FindAll(배열, 조건람다)`**는 조건이 참인 원소만 새 배열로 모읍니다. 로직이 길어지면 람다 대신 별도 메서드가 읽기 쉽습니다.

| 용어 | 의미 |
|---|---|
| 람다 | 이름 없이 즉시 전달해 사용하는 함수 표현식 |
| `Array.Sort` | 비교 기준으로 배열 원소 순서를 바꾸는 메서드 |
| `Array.FindAll` | 조건에 맞는 원소만 모아 새 배열을 만드는 메서드 |

아래를 실행해 정렬된 `1 3 5 8` 출력을 확인해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] nums = { 3, 1, 8, 5 };
        Array.Sort(nums, (a, b) => a.CompareTo(b));
        foreach (int x in nums)
        {
            Console.Write(x + " ");
        }
    }
}
```

**예상 출력**

```text
1 3 5 8 
```

**코드 해석**
- `Sort`에 `(a, b) => a.CompareTo(b)`를 넘겨 오름차순으로 정렬합니다.
- `CompareTo`는 앞 장 제네릭에서 본 비교 계약과 같습니다.
- `foreach`로 정렬된 배열을 공백과 함께 출력합니다.

이어서 짝수만 고르는 필터입니다. 아래를 실행해 `2 4 6`이 나오는지 확인해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] nums = { 1, 2, 3, 4, 6 };
        int[] evens = Array.FindAll(nums, x => x % 2 == 0);
        foreach (int x in evens)
        {
            Console.Write(x + " ");
        }
    }
}
```

**예상 출력**

```text
2 4 6 
```

**코드 해석**
- `x => x % 2 == 0`은 짝수일 때 참인 조건 람다입니다.
- `FindAll`이 조건이 참인 값만 `evens` 배열에 담습니다.
- `foreach`로 결과를 출력합니다.

### 연습문제

**문제 1**
- 문제: 배열에서 짝수만 골라 출력하세요.
- 입력: 없음. 예 배열 `{ 1, 2, 3, 4, 6 }`
- 출력: `2 4 6` 처럼 공백으로 구분
- 조건: 람다와 `Array.FindAll` 사용

**문제 2**
- 문제: 문자열 배열을 길이 순으로 정렬해 출력하세요.
- 입력: 없음. 예 `{ "hi", "a", "hello" }`
- 출력: `a hi hello` 처럼 짧은 것부터
- 조건: `Array.Sort`와 `(a, b) => a.Length.CompareTo(b.Length)` 형태

**문제 3**
- 문제: `Func<int, int>`에 “1을 더하는” 람다를 담아 `10`을 넣은 결과를 출력하세요.
- 입력: 없음
- 출력: `11`
- 조건: `Func<int, int>`와 `=>` 사용

### 정답 포인트

- 문제 1: `Array.FindAll(nums, x => x % 2 == 0)` 후 `foreach`로 출력
- 문제 2: `Array.Sort(words, (a, b) => a.Length.CompareTo(b.Length));`
- 문제 3: `Func<int, int> addOne = x => x + 1; Console.WriteLine(addOne(10));`
- 공통: 짧고 목적이 분명할 때 람다, 복잡하면 메서드로 분리

---

[상위 문서로 돌아가기](./README.md)
