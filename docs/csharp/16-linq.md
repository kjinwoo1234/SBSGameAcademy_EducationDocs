# Chapter 16 LINQ

## 학습 목표
- LINQ의 기본 개념과 쿼리 메서드를 이해한다.
- 컬렉션 데이터를 선언적으로 조회/변환할 수 있다.

## 세부 주제
### 16-1 LINQ 기본
- `Where`, `Select`

### 16-2 정렬/집계
- `OrderBy`, `Count`, `Sum`

### 16-3 즉시 실행 vs 지연 실행
- 실행 시점 이해

## 실습 체크리스트
- 점수 목록에서 조건 필터 + 정렬 + 평균 계산을 수행한다.

## 본문

### 16-1 LINQ 기본
- LINQ는 컬렉션 데이터를 일관된 문법으로 처리하는 기능입니다.
- `Where`는 필터링, `Select`는 변환에 사용합니다.

### 16-2 정렬/집계
- `OrderBy`로 정렬하고 `Count`, `Sum`, `Average`로 집계를 수행합니다.

### 16-3 즉시 실행 vs 지연 실행
- 일부 LINQ 쿼리는 실제 순회 시점에 실행됩니다(지연 실행).
- 결과를 고정하려면 `ToList()`로 즉시 실행해 저장합니다.

약어 설명(LINQ):
- **LINQ**는 **Language Integrated Query**의 약자입니다.
- 한국어로는 "언어 통합 질의"이며, C# 문법 안에서 데이터 조회/가공을 수행하는 기능입니다.
- SQL처럼 데이터를 다루되, C# 코드와 자연스럽게 결합되는 것이 핵심입니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 지연 실행(deferred execution) | 결과가 실제 순회 시점에 계산되는 실행 방식 |
| 즉시 실행(immediate execution) | `ToList()`처럼 그 자리에서 결과를 확정하는 실행 방식 |

예시 코드:
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

예상 출력:
```text
95, 88, 82
```

코드 해석:
- `Where`로 80점 이상만 필터링하고 `OrderByDescending`으로 내림차순 정렬합니다.
- `ToList()`를 호출해 결과를 즉시 리스트로 확정합니다.

연습문제:
1. 문제 1 - 성인 필터
   - 문제: 나이 리스트에서 20세 이상만 출력하세요.
   - 입력: 나이 리스트
   - 출력: 필터 결과
   - 조건: `Where` 사용
2. 문제 2 - 이름 길이 변환
   - 문제: 이름 리스트를 길이 리스트로 변환해 출력하세요.
   - 입력: 문자열 리스트
   - 출력: 길이 리스트
   - 힌트: `Select`

정답 포인트:
- LINQ는 데이터 처리 의도를 짧게 표현
- 지연 실행 특성을 이해하고 필요 시 `ToList()` 사용

---

[상위 문서로 돌아가기](./README.md)
