# C# static

`static` 멤버는 객체 인스턴스가 아닌 클래스 자체에 속합니다.  
공용 유틸리티 함수나 전역 카운터 같은 공유 데이터에 적합합니다.

## 1) static 메서드
```csharp
class Calculator
{
    public static int Add(int a, int b) => a + b;
}
int r = Calculator.Add(10, 20);
```

## 2) static 필드
```csharp
class Student
{
    public static int Count;
    public Student() { Count++; }
}
```

## 3) 사용 시 주의점
- static 내부에서는 인스턴스 필드 직접 사용 불가
- 테스트 시 전역 상태 오염 주의

## 4) 연습 문제
1. 생성된 객체 수를 static 필드로 관리하세요.
2. 문자열 유틸리티 static 클래스를 작성하세요.

---

[상위 문서로 돌아가기](./README.md)
