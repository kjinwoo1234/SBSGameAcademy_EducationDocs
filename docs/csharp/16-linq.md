# C# LINQ

LINQ는 배열/리스트 같은 데이터를 "검색, 정렬, 변환"할 때 쓰는 문법입니다.  
초보자 기준으로는 **for문으로 하던 데이터 처리 코드를 더 읽기 좋게 바꾸는 도구**로 이해하면 쉽습니다.

## 1) 왜 LINQ를 쓰는가?
반복문으로도 구현은 가능하지만, 조건이 많아질수록 코드가 길고 복잡해집니다.  
LINQ는 "어떻게 반복할지"보다 "무엇을 뽑을지"에 집중해서 작성합니다.

## 2) 자주 쓰는 메서드 (초보자 설명)
- `Where`: 조건에 맞는 데이터만 고르기
- `Select`: 데이터 모양 바꾸기 (예: 학생 객체 -> 이름 문자열)
- `OrderBy` / `OrderByDescending`: 정렬하기
- `ToList` / `ToArray`: 최종 결과를 리스트/배열로 확정하기

## 3) for문 방식과 비교
### (1) for/foreach 방식
```csharp
int[] scores = { 95, 70, 80, 100 };
List<int> highScores = new List<int>();

foreach (int s in scores)
{
    if (s >= 80)
        highScores.Add(s);
}

highScores.Sort();
highScores.Reverse(); // 내림차순
```

### (2) LINQ 방식
```csharp
using System.Linq;

int[] scores = { 95, 70, 80, 100 };
List<int> highScores = scores
    .Where(s => s >= 80)             // 80점 이상만 선택
    .OrderByDescending(s => s)       // 큰 값부터 정렬
    .ToList();                       // 리스트로 확정
```

## 4) 객체 리스트 실전 예제
```csharp
class Student
{
    public string Name { get; set; }
    public int Age { get; set; }
}

List<Student> students = new List<Student>
{
    new Student { Name = "홍길동", Age = 20 },
    new Student { Name = "이순신", Age = 30 },
    new Student { Name = "강감찬", Age = 15 }
};

List<string> adultNames = students
    .Where(s => s.Age >= 20)
    .Select(s => s.Name)
    .ToList();
```

## 5) 자주 하는 실수
- `using System.Linq;` 누락
- `Where`와 `Select` 순서를 헷갈림
- `ToList()` 없이 결과를 바로 수정하려고 함

## 6) 연습 문제
1. 정수 배열에서 짝수만 골라 오름차순 리스트로 만드세요.
2. 학생 리스트에서 20세 이상 이름만 추출하세요.
3. 점수 배열에서 60점 미만 점수 개수를 구하세요.

---

[상위 문서로 돌아가기](./README.md)
