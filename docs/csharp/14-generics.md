# C# 제네릭

## 핵심
- 타입을 고정하지 않고 재사용 가능한 코드 작성
- 대표 예시: `List<T>`, 제네릭 메서드

## 예제
```csharp
List<int> numbers = new List<int> { 1, 2, 3 };
static void Print<T>(T value) => Console.WriteLine(value);
```

---

[상위 문서로 돌아가기](./README.md)
