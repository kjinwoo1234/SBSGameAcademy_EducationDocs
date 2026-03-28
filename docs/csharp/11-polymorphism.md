# C# 다형성

다형성은 동일한 인터페이스로 서로 다른 구현을 실행할 수 있게 합니다.  
확장 가능한 구조(플러그인, 전략 패턴 등)의 핵심 개념입니다.

## 1) 오버라이딩
```csharp
class Person
{
    public virtual void Work() => Console.WriteLine("일");
}

class Developer : Person
{
    public override void Work() => Console.WriteLine("코딩");
}
```

## 2) 부모 타입 참조
```csharp
Person p = new Developer();
p.Work(); // 코딩
```

## 3) 추상 클래스와 인터페이스
- 추상 클래스: 일부 공통 구현 + 강제 규약
- 인터페이스: 순수 계약 정의

## 4) 연습 문제
1. `Shape` 추상 클래스로 원/사각형 넓이 계산
2. `IMovable` 인터페이스 구현 클래스 작성

---

[상위 문서로 돌아가기](./README.md)
