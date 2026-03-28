# C# 제네릭

제네릭은 "자료형만 바뀌고 로직은 같은 코드"를 하나로 묶는 방법입니다.  
쉽게 말해, **int용/ string용 코드를 따로 만들지 않게 해주는 기능**입니다.

## 1) 왜 필요한가?
예를 들어 출력 함수가 아래처럼 있다면:
```csharp
static void PrintInt(int value) => Console.WriteLine(value);
static void PrintString(string value) => Console.WriteLine(value);
```
로직은 같은데 타입만 다릅니다. 이럴 때 제네릭이 유용합니다.

## 2) 제네릭 컬렉션
```csharp
List<int> numbers = new List<int> { 1, 2, 3 };
List<string> names = new List<string> { "홍길동", "이순신" };
```

## 3) 제네릭 메서드
```csharp
static void Print<T>(T value)
{
    Console.WriteLine(value);
}
```

`T`는 "아직 정해지지 않은 타입"이라는 의미입니다.  
사용 시점에 `T`가 `int`, `string` 등으로 결정됩니다.

## 4) 타입 제약 (`where`)
```csharp
static T Max<T>(T a, T b) where T : IComparable<T>
{
    return a.CompareTo(b) >= 0 ? a : b;
}
```

위 코드는 비교(`CompareTo`)가 가능한 타입만 허용합니다.

## 5) 초보자 실수 포인트
- `T`를 아무 타입이나 쓸 수 있다고 오해 (제약이 필요할 수 있음)
- 제네릭이 "느려진다"고 오해 (대부분 타입 안정성/유지보수성 이점이 큼)

## 6) 연습 문제
1. 제네릭으로 최대값 함수 작성
2. `List<T>` 기반 공통 출력 유틸리티 작성

---

[상위 문서로 돌아가기](./README.md)
