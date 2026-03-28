# C# 델리게이트

## 핵심
- 메서드를 변수처럼 저장/전달
- `Action`, `Func`가 대표적인 표준 델리게이트

## 예제
```csharp
Action<string> show = msg => Console.WriteLine(msg);
show("안녕하세요");
```

---

[상위 문서로 돌아가기](./README.md)
