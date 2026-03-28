# C# 람다식

## 핵심
- 이름 없는 함수를 `=>`로 간결하게 표현
- 콜백, LINQ, 이벤트 처리에 자주 사용

## 예제
```csharp
Func<int, bool> isEven = n => n % 2 == 0;
Console.WriteLine(isEven(10));
```

---

[상위 문서로 돌아가기](./README.md)
