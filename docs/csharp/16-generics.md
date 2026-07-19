# Chapter 16 제네릭

## 학습 목표
- 제네릭 메서드·클래스의 `T` 문법을 사용할 수 있다.
- 제네릭이 타입 안전·재사용에 주는 이점을 구분한다.
- `where` 제약으로 허용 타입을 제한할 수 있다.

## 본문

### 16-1 제네릭 메서드

**제네릭**은 타입만 다르고 로직은 같은 코드를 **`T` 같은 타입 매개변수**로 한 번 작성하는 방법입니다. `Swap<T>(ref T a, ref T b)`처럼 쓰면 호출할 때 `T`가 `int`면 정수 두 개를, `string`이면 문자열 두 개를 바꿉니다. `object`로 받고 캐스팅하는 방식보다 **컴파일 시점 타입 검사**가 강해집니다.

| 방식 | 예 | 장점 | 단점 |
|---|---|---|---|
| 타입별 메서드 | `SwapInt`, `SwapString` | 단순 | 중복 많음 |
| 제네릭 | `Swap<T>` | 한 구현 재사용 | 문법 학습 필요 |

아래를 `Main`에 넣고 실행해, 같은 `Swap`이 `int`와 `string`에 쓰이는지 확인해 보세요. `ref`는 앞 장 메서드에서 배운 “원본을 바꾸는 전달”입니다.

```csharp
using System;

class Program
{
    static void Swap<T>(ref T a, ref T b)
    {
        T temp = a;
        a = b;
        b = temp;
    }

    static void Main()
    {
        int x = 3;
        int y = 7;
        Swap(ref x, ref y);
        Console.WriteLine($"{x}, {y}");

        string s1 = "aa";
        string s2 = "bb";
        Swap(ref s1, ref s2);
        Console.WriteLine($"{s1}, {s2}");
    }
}
```

**예상 출력**

```text
7, 3
bb, aa
```

**코드 해석**
- `Swap<T>`의 `T`는 호출 인자에 맞춰 `int` 또는 `string`으로 채워집니다.
- `temp`에 한쪽을 잠시 저장한 뒤 두 값을 바꿉니다.
- 메서드 본문은 하나인데 두 타입에 재사용됩니다.

### 16-2 제네릭 클래스

제네릭은 클래스에도 붙일 수 있습니다. `Box<T>`처럼 **같은 구조·다른 보관 타입**을 `T`로 받습니다. `Box<int>`는 정수를, `Box<string>`은 문자열을 담습니다. 잘못 넣은 타입은 실행 전이 아니라 **컴파일 오류**로 막는 경우가 많습니다.

`List<int>`, `List<string>`도 같은 **`List<T>`** 패턴입니다. 리스트의 여러 메서드는 이후 컬렉션 장에서 다루고, 이 장에서는 “원소 타입만 바꿔 쓰는 제네릭 클래스”라는 점만 기억하면 됩니다.

아래를 실행해 `Box<int>`에 넣은 값이 그대로 출력되는지 확인해 보세요.

```csharp
using System;

class Box<T>
{
    public T Value { get; set; }
}

class Program
{
    static void Main()
    {
        Box<int> numberBox = new Box<int>();
        numberBox.Value = 10;
        Console.WriteLine(numberBox.Value);

        Box<string> nameBox = new Box<string>();
        nameBox.Value = "Kim";
        Console.WriteLine(nameBox.Value);
    }
}
```

**예상 출력**

```text
10
Kim
```

**코드 해석**
- `Box<T>`는 보관 타입만 `T`로 비워 둔 상자입니다.
- `Box<int>`와 `Box<string>`은 구조는 같고 원소 타입만 다릅니다.
- `Value` 프로퍼티로 값을 넣고 읽습니다.

### 16-3 제약 조건

`where T : IComparable<T>`처럼 **`where`**로 “이 타입은 비교 가능해야 함”을 강제할 수 있습니다. 이를 **제약 조건**이라고 합니다. `IComparable<T>`는 앞 장 인터페이스처럼 계약이고, `CompareTo`로 대소·순서를 알려 줍니다. 제약이 없으면 `T`에 `CompareTo`를 쓸 수 없어 컴파일이 거절합니다.

| 용어 | 의미 |
|---|---|
| 제네릭 | 타입에 독립적인 코드를 작성하는 방식 |
| 제약 조건 | 제네릭에 허용되는 타입 범위를 제한하는 규칙 |

아래를 실행해 `Max(3, 7)`과 `Max("aa", "ab")` 결과를 확인해 보세요.

```csharp
using System;

class Program
{
    static T Max<T>(T a, T b) where T : IComparable<T>
    {
        if (a.CompareTo(b) >= 0)
        {
            return a;
        }
        else
        {
            return b;
        }
    }

    static void Main()
    {
        Console.WriteLine(Max(3, 7));
        Console.WriteLine(Max("aa", "ab"));
    }
}
```

**예상 출력**

```text
7
ab
```

**코드 해석**
- `where T : IComparable<T>` 때문에 비교 가능한 타입만 `Max`에 올 수 있습니다.
- `CompareTo`가 0 이상이면 `a`, 아니면 `b`를 반환합니다. `if`/`else`로 분기합니다.
- 같은 메서드가 `int`와 `string`에 재사용됩니다.

### 연습문제

**문제 1**
- 문제: 두 값 중 작은 값을 반환하는 제네릭 메서드 `Min`을 작성하세요.
- 입력: 없음. 예 `Min(3, 7)`, `Min("aa", "ab")`
- 출력:
  ```text
  3
  aa
  ```
- 조건: `where T : IComparable<T>`, `if`/`else`로 비교

**문제 2**
- 문제: 값을 저장·반환하는 `Box<T>` 클래스를 작성하세요.
- 입력: 없음. 예 `Box<int>`에 `10` 저장 후 출력
- 출력: `10`
- 조건: 제네릭 필드 또는 프로퍼티 + 저장·조회

**문제 3**
- 문제: 두 값을 바꾸는 제네릭 메서드 `Swap`을 작성하세요.
- 입력: 없음. 예 `x=1`, `y=2`를 바꾼 뒤 출력
- 출력: `2, 1`
- 조건: `Swap<T>(ref T a, ref T b)` 형태

### 정답 포인트

- 문제 1: `CompareTo`가 0보다 작으면 `a`, 아니면 `b`. Max와 반대. `if`/`else` 사용
- 문제 2: `class Box<T> { public T Value { get; set; } }`
- 문제 3: `T temp = a; a = b; b = temp;`
- 공통: 제네릭으로 재사용·타입 안전, 제약은 잘못된 타입을 일찍 차단

---

[상위 문서로 돌아가기](./README.md)
