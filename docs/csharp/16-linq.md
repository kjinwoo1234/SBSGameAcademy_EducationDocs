# C# LINQ

## 핵심
- 컬렉션 데이터를 선언형으로 필터/정렬/선택
- 자주 쓰는 메서드: `Where`, `Select`, `OrderBy`

## 예제
```csharp
int[] scores = { 95, 70, 80 };
var high = scores.Where(s => s >= 80).OrderByDescending(s => s).ToList();
```

---

[상위 문서로 돌아가기](./README.md)
