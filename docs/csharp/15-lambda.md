# C# 람다식

람다식은 익명 함수를 간결하게 표현하는 문법입니다.  
LINQ, 이벤트 핸들러, 비동기 콜백에서 매우 자주 사용됩니다.

## 1) 기본 문법
```csharp
Func<int, bool> isEven = n => n % 2 == 0;
Console.WriteLine(isEven(10));
```

## 2) 매개변수/본문 형태
```csharp
Action<string> greet = name => Console.WriteLine($"안녕, {name}");
Func<int, int, int> add = (x, y) => x + y;
```

## 3) 메서드 그룹 변환
```csharp
bool IsOdd(int n) => n % 2 == 1;
Func<int, bool> odd = IsOdd;
```

## 4) 연습 문제
1. 람다식으로 제곱 함수 작성
2. 문자열 리스트에서 길이 3 이상만 출력

---

[상위 문서로 돌아가기](./README.md)
