# C# 제네릭

제네릭은 타입을 일반화해 코드 재사용성과 타입 안전성을 동시에 확보합니다.  
중복 클래스를 만들 필요가 줄어 실무 생산성이 크게 향상됩니다.

## 1) 제네릭 컬렉션
```csharp
List<int> numbers = new List<int> { 1, 2, 3 };
List<string> names = new List<string> { "홍길동", "이순신" };
```

## 2) 제네릭 메서드
```csharp
static void Print<T>(T value)
{
    Console.WriteLine(value);
}
```

## 3) 타입 제약(where)
```csharp
static T Max<T>(T a, T b) where T : IComparable<T>
{
    return a.CompareTo(b) >= 0 ? a : b;
}
```

## 4) 연습 문제
1. 제네릭으로 최대값 함수 작성
2. `List<T>` 기반 공통 출력 유틸리티 작성

---

[상위 문서로 돌아가기](./README.md)
