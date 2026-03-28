# C# 메서드

메서드는 특정 기능을 캡슐화한 코드 블록입니다.  
중복 코드를 줄이고 테스트 가능한 구조를 만드는 핵심입니다.

## 1) 기본 메서드
```csharp
static int Add(int a, int b)
{
    return a + b;
}
```

## 2) `void` 메서드와 반환 메서드
```csharp
static void PrintName(string name)
{
    Console.WriteLine(name);
}
```

## 3) `out` 매개변수
```csharp
static bool TryDivide(int a, int b, out int result)
{
    if (b == 0)
    {
        result = 0;
        return false;
    }
    result = a / b;
    return true;
}
```

## 4) 연습 문제
1. 세 수 평균을 반환하는 메서드 작성
2. 입력 검증 로직을 메서드로 분리

---

[상위 문서로 돌아가기](./README.md)
