# C# 다형성

## 핵심
- 부모 타입으로 여러 자식 객체를 다룸
- 실제 동작은 자식의 오버라이딩 메서드가 수행

## 예제
```csharp
class Person { public virtual void Work() { } }
class Developer : Person { public override void Work() { Console.WriteLine("코딩"); } }
Person p = new Developer();
p.Work();
```

---

[상위 문서로 돌아가기](./README.md)
