# C# 클래스와 객체

클래스는 데이터와 동작을 묶는 설계도이고, 객체는 그 설계도로 만든 실체입니다.  
객체지향 설계의 시작점이며 이후 상속/다형성의 기반이 됩니다.

## 1) 기본 클래스
```csharp
class Student
{
    public string Name;
    public int Score;

    public void Introduce()
    {
        Console.WriteLine($"{Name}, 점수: {Score}");
    }
}
```

## 2) 객체 생성
```csharp
Student s = new Student();
s.Name = "홍길동";
s.Score = 95;
s.Introduce();
```

## 3) 생성자
```csharp
class Student
{
    public string Name;
    public Student(string name) { Name = name; }
}
```

## 4) 연습 문제
1. `Player` 클래스를 만들고 체력 감소 메서드를 구현하세요.
2. 생성자를 통해 필수값을 초기화하도록 수정하세요.

---

[상위 문서로 돌아가기](./README.md)
