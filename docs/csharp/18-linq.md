# Chapter 18 LINQ

## 학습 목표
- LINQ의 기본 개념과 쿼리 메서드를 이해한다.
- 컬렉션 데이터를 선언적으로 조회/변환할 수 있다.
- 지연 실행과 `ToList()` 즉시 확정을 구분한다.

## 본문

### 18-1 LINQ 기본

**LINQ**(Language Integrated Query, 언어 통합 질의)는 컬렉션 등을 C# 문법 안에서 **조회·가공**하는 기능입니다. **`Where`**는 조건에 맞는 것만 남기고, **`Select`**는 각 원소를 다른 형태로 바꿉니다.

| 메서드 | 역할 | 예 |
|---|---|---|
| `Where` | 필터 | `x => x >= 80` |
| `Select` | 변환 | `x => x.Length` |

### 18-2 정렬/집계

**`OrderBy` / `OrderByDescending`**으로 정렬하고, **`Count`**, **`Sum`**, **`Average`**로 개수·합·평균을 구합니다. 필터 → 정렬 → 집계 순으로 체이닝하는 경우가 많습니다.

### 18-3 즉시 실행 vs 지연 실행

많은 LINQ 호출은 **지연 실행**이라, 실제로 `foreach`하거나 `ToList()`할 때 계산됩니다. 결과를 **그 자리에서 리스트로 고정**하려면 `ToList()`로 **즉시 실행**합니다.

| 방식 | 의미 | 예 |
|---|---|---|
| 지연 실행 | 순회 시점에 계산 | `Where`만 연결 |
| 즉시 실행 | 바로 결과 확정 | `ToList()`, `Count()` |

| 용어 | 의미 |
|---|---|
| 지연 실행(deferred execution) | 결과가 실제 순회 시점에 계산되는 실행 방식 |
| 즉시 실행(immediate execution) | `ToList()`처럼 그 자리에서 결과를 확정하는 실행 방식 |

아래 코드를 실행해 80점 이상만 내림차순으로 출력되는지 확인해 보세요. (`using System.Linq` 필요)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main()
    {
        List<int> scores = new List<int> { 70, 95, 82, 60, 88 };
        var result = scores.Where(x => x >= 80).OrderByDescending(x => x).ToList();
        Console.WriteLine(string.Join(", ", result));
    }
}
```

**예상 출력**

```text
95, 88, 82
```

**코드 해석**
- `Where`로 80 이상만 남깁니다.
- `OrderByDescending`으로 큰 점수부터 정렬합니다.
- `ToList()`로 결과를 즉시 리스트에 담아 `Join`으로 출력합니다.

### 연습문제

**문제 1**
- 문제: 나이 리스트에서 20세 이상만 출력하세요.
- 입력: 없음(예: `{ 15, 20, 25, 18 }`)
- 출력: `20, 25` (형식 유사 OK)
- 조건: `Where` 사용

**문제 2**
- 문제: 이름 리스트를 길이 리스트로 변환해 출력하세요.
- 입력: 없음(예: `{ "Ann", "Bob", "Chris" }`)
- 출력: `3, 3, 5`
- 조건: `Select` 사용

### 정답 포인트

- 문제 1: `ages.Where(a => a >= 20)` + 출력
- 문제 2: `names.Select(n => n.Length)` + `string.Join`
- 공통: 의도(필터/변환)를 LINQ로 짧게, 필요 시 `ToList()`

---

[상위 문서로 돌아가기](./README.md)
