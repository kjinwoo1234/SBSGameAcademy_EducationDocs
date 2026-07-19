# Chapter 18 LINQ

## 학습 목표
- `Where`·`Select`로 배열을 필터·변환할 수 있다.
- `OrderBy`·`Count`로 정렬·개수를 구할 수 있다.
- 지연 실행과 `ToArray()` 즉시 확정을 구분한다.

## 본문

### 18-1 LINQ 기본

**LINQ**는 언어 안에 넣는 조회 기능입니다. 배열 같은 컬렉션을 C# 문법으로 **조회·가공**할 때 씁니다. **`Where`**는 조건에 맞는 것만 남기고, **`Select`**는 각 원소를 다른 형태로 바꿉니다. 사용하려면 파일 위에 `using System.Linq;`가 필요합니다. 조건·변환에는 앞 장 람다를 넘깁니다.

| 메서드 | 역할 | 예 |
|---|---|---|
| `Where` | 필터 | `x => x >= 80` |
| `Select` | 변환 | `x => x.Length` |

먼저 필터입니다. 아래를 `Main`에 넣고 실행해 80 이상만 출력되는지 확인해 보세요.

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        int[] scores = { 70, 95, 82, 60, 88 };
        var over80 = scores.Where(x => x >= 80);
        foreach (int s in over80)
        {
            Console.WriteLine(s);
        }
    }
}
```

**예상 출력**

```text
95
82
88
```

**코드 해석**
- `Where(x => x >= 80)`은 80 이상인 점수만 남깁니다.
- 결과는 `foreach`로 한 개씩 출력합니다.
- `var over80`의 실제 타입은 LINQ가 만든 조회 결과입니다. 입문에서는 `var`로 받아도 됩니다.

이어서 변환입니다. 문자열 배열을 길이 숫자로 바꿔 출력해 보세요.

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        string[] names = { "Ann", "Bob", "Chris" };
        var lengths = names.Select(n => n.Length);
        foreach (int len in lengths)
        {
            Console.WriteLine(len);
        }
    }
}
```

**예상 출력**

```text
3
3
5
```

**코드 해석**
- `Select(n => n.Length)`는 각 이름을 글자 수로 바꿉니다.
- `foreach`로 `3`, `3`, `5`를 한 줄씩 출력합니다.

### 18-2 정렬과 집계

**`OrderBy`**는 오름차순, **`OrderByDescending`**은 내림차순으로 정렬합니다. **`Count()`**는 개수, **`Sum()`**은 합입니다. 필터 뒤에 정렬이나 집계를 이어 붙이는 경우가 많습니다.

아래를 실행해 80 이상을 큰 점수부터 출력하는지 확인해 보세요.

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        int[] scores = { 70, 95, 82, 60, 88 };
        var result = scores.Where(x => x >= 80).OrderByDescending(x => x);
        foreach (int s in result)
        {
            Console.WriteLine(s);
        }
        Console.WriteLine(scores.Count(x => x >= 80));
    }
}
```

**예상 출력**

```text
95
88
82
3
```

**코드 해석**
- `Where`로 80 이상만 남긴 뒤 `OrderByDescending`으로 큰 값부터 정렬합니다.
- `Count(x => x >= 80)`은 조건을 만족하는 개수 `3`을 바로 구합니다.
- 정렬 결과는 `foreach`로 출력합니다.

### 18-3 즉시 실행과 지연 실행

많은 LINQ 호출은 **지연 실행**이라, 실제로 `foreach`하거나 결과를 배열로 고정할 때 계산됩니다. **`ToArray()`**를 호출하면 그 자리에서 배열로 확정하는 **즉시 실행**이 됩니다. `ToList()`도 같은 역할이지만, 리스트 문법은 이후 컬렉션 장에서 다루므로 이 장에서는 `ToArray()`를 씁니다.

| 방식 | 의미 | 예 |
|---|---|---|
| 지연 실행 | 순회 시점에 계산 | `Where`만 연결 |
| 즉시 실행 | 바로 결과 확정 | `ToArray()`, `Count()` |

| 용어 | 의미 |
|---|---|
| 지연 실행 | 결과가 실제 순회 시점에 계산되는 방식 |
| 즉시 실행 | `ToArray()`처럼 그 자리에서 결과를 확정하는 방식 |

아래를 실행해, `ToArray()`로 고정한 뒤 원본 배열을 바꿔도 출력은 예전 값인지 확인해 보세요.

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        int[] scores = { 70, 95, 82 };
        int[] snapshot = scores.Where(x => x >= 80).ToArray();

        scores[1] = 50;

        foreach (int s in snapshot)
        {
            Console.WriteLine(s);
        }
    }
}
```

**예상 출력**

```text
95
82
```

**코드 해석**
- `ToArray()` 시점에 80 이상인 `95`, `82`가 새 배열에 복사됩니다.
- 그 다음 원본 `scores[1]`을 `50`으로 바꿔도 `snapshot`은 그대로입니다.
- `ToArray()` 없이 `Where`만 두고 나중에 `foreach`하면, 순회 직전 원본 상태가 반영되는 지연 실행과 대비됩니다.

### 연습문제

**문제 1**
- 문제: 나이 배열에서 20세 이상만 출력하세요.
- 입력: 없음. 예 `{ 15, 20, 25, 18 }`
- 출력:
  ```text
  20
  25
  ```
- 조건: `Where`와 `foreach` 사용

**문제 2**
- 문제: 이름 배열을 길이 배열로 변환해 출력하세요.
- 입력: 없음. 예 `{ "Ann", "Bob", "Chris" }`
- 출력:
  ```text
  3
  3
  5
  ```
- 조건: `Select`와 `foreach` 사용

**문제 3**
- 문제: 점수 배열에서 60 이상인 개수를 출력하세요.
- 입력: 없음. 예 `{ 50, 70, 90, 40 }`
- 출력: `2`
- 조건: `Count`와 람다 조건 사용

### 정답 포인트

- 문제 1: `ages.Where(a => a >= 20)` 후 `foreach`로 출력
- 문제 2: `names.Select(n => n.Length)` 후 `foreach`로 출력
- 문제 3: `Console.WriteLine(scores.Count(s => s >= 60));`
- 공통: 필터는 `Where`, 변환은 `Select`, 개수는 `Count`. 결과를 고정할 때는 `ToArray()`

---

[상위 문서로 돌아가기](./README.md)
