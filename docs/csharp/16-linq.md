# C# LINQ

LINQ는 컬렉션 데이터를 선언형으로 질의하는 기능입니다.  
복잡한 반복문을 간결하게 바꾸고, 가독성 높은 데이터 처리 코드를 만들 수 있습니다.

## 1) 주요 메서드
- `Where`: 필터링
- `Select`: 변환
- `OrderBy` / `OrderByDescending`: 정렬
- `ToList` / `ToArray`: 결과 materialize

## 2) 기본 예제
```csharp
using System.Linq;

int[] scores = { 95, 70, 80, 100 };
var high = scores
    .Where(s => s >= 80)
    .OrderByDescending(s => s)
    .ToList();
```

## 3) 객체 리스트 처리
```csharp
var names = students
    .Where(s => s.Age >= 20)
    .Select(s => s.Name);
```

## 4) 연습 문제
1. 짝수만 추출해 오름차순 정렬
2. 학생 목록에서 성인 이름 리스트 생성

---

[상위 문서로 돌아가기](./README.md)
