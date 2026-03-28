# C# 상속

상속은 기존 클래스의 속성과 기능을 재사용하는 메커니즘입니다.  
중복 코드를 줄이고 타입 계층을 설계할 수 있습니다.

## 1) 기본 상속
```csharp
class Person
{
    public string Name { get; set; }
    public void Eat() => Console.WriteLine("식사");
}

class Student : Person
{
    public void Study() => Console.WriteLine("공부");
}
```

## 2) `base` 키워드
부모 생성자/멤버를 명시적으로 호출할 때 사용합니다.

```csharp
class Student : Person
{
    public Student(string name) { Name = name; }
}
```

## 3) 연습 문제
1. `Animal`-`Dog` 상속 구조 설계
2. 부모 공통 메서드를 자식에서 재사용

---

[상위 문서로 돌아가기](./README.md)
