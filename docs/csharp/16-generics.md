# Chapter 16 제네릭

## 학습 목표
- 제네릭의 목적과 문법을 이해한다.
- 타입 안전성과 재사용성의 이점을 설명할 수 있다.
- `where` 제약으로 허용 타입을 제한할 수 있다.

## 본문

### 16-1 제네릭 메서드

**제네릭(generic)**은 “타입만 다르고 로직은 같은” 코드를 **`T` 같은 타입 매개변수**로 한 번 작성하는 방법입니다. `Max<T>(T a, T b)`는 호출할 때 `T`가 `int`면 정수 비교, `string`이면 문자열 비교로 채워집니다. `object`로 받고 캐스팅하는 방식보다 **컴파일 시점 타입 검사**가 강해집니다.

| 방식 | 예 | 장점 | 단점 |
|---|---|---|---|
| 타입별 메서드 | `MaxInt`, `MaxString` | 단순 | 중복 많음 |
| 제네릭 | `Max<T>` | 한 구현 재사용 | 제약·문법 학습 필요 |

### 16-2 제네릭 클래스

`List<int>`, `List<string>`처럼 **같은 구조·다른 원소 타입**을 `List<T>` 패턴으로 재사용합니다. 잘못 넣은 타입은 실행 전이 아니라 **컴파일 오류**로 막는 경우가 많습니다.

### 16-3 제약 조건

`where T : IComparable<T>`처럼 **`where`**로 “이 타입은 비교 가능해야 함”을 강제할 수 있습니다. `IComparable<T>`는 `CompareTo`로 대소/순서를 알려 주는 계약입니다. 제약이 없으면 `T`에 `CompareTo`를 쓸 수 없어 컴파일이 거절합니다.

| 용어 | 의미 |
|---|---|
| 제네릭(generic) | 타입에 독립적인 코드를 작성하는 방식 |
| 제약 조건(constraint) | 제네릭 타입에 허용되는 타입 범위를 제한하는 규칙 |

아래 코드를 실행해 `Max(3, 7)`과 `Max("aa", "ab")` 결과를 확인해 보세요.

```csharp
using System;

class Program
{
    static T Max<T>(T a, T b) where T : IComparable<T>
    {
        return a.CompareTo(b) >= 0 ? a : b;
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
- `CompareTo`가 0 이상이면 `a`, 아니면 `b`를 반환합니다.
- 같은 메서드가 `int`와 `string`에 재사용됩니다.

### 연습문제

**문제 1**
- 문제: 두 값 중 작은 값을 반환하는 제네릭 메서드 `Min`을 작성하세요.
- 입력: 없음(예: `Min(3, 7)`, `Min("aa", "ab")`)
- 출력:
  ```text
  3
  aa
  ```
- 조건: `where T : IComparable<T>`

**문제 2**
- 문제: 값을 저장/반환하는 `Box<T>` 클래스를 작성하세요.
- 입력: 없음(예: `Box<int>`에 `10` 저장 후 출력)
- 출력: `10`
- 조건: 제네릭 필드(또는 프로퍼티) + 저장/조회 멤버

### 정답 포인트

- 문제 1: `CompareTo`가 0보다 작으면 `a`, 아니면 `b` (Max와 반대)
- 문제 2: `class Box<T> { public T Value { get; set; } }`
- 공통: 제네릭으로 재사용·타입 안전, 제약은 잘못된 타입을 일찍 차단

---

[상위 문서로 돌아가기](./README.md)
