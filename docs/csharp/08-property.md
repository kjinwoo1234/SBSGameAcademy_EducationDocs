# C# 프로퍼티

프로퍼티는 필드를 캡슐화하면서 외부에 안전한 접근 통로를 제공합니다.  
실무에서는 필드 공개보다 프로퍼티 사용이 기본입니다.

## 1) 기본 프로퍼티
```csharp
private int age;
public int Age
{
    get => age;
    set => age = value < 0 ? 0 : value;
}
```

## 2) 자동 구현 프로퍼티
```csharp
public string Name { get; set; }
public int Score { get; private set; }
```

## 3) 읽기 전용 프로퍼티
```csharp
public string Id { get; }
public Person(string id) { Id = id; }
```

## 4) 연습 문제
1. 점수 범위(0~100) 검증 프로퍼티 작성
2. 읽기 전용 ID 프로퍼티를 가진 클래스 작성

---

[상위 문서로 돌아가기](./README.md)
