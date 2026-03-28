# C# 람다식

람다식은 "이름 없는 짧은 함수"를 만드는 문법입니다.  
처음에는 낯설지만, 익숙해지면 코드가 짧아지고 읽기 쉬워집니다.

## 1) 먼저 일반 함수 방식
```csharp
bool IsEven(int n)
{
    return n % 2 == 0;
}
```

## 2) 같은 코드를 람다식으로
```csharp
Func<int, bool> isEven = n => n % 2 == 0;
Console.WriteLine(isEven(10));
```

`n => n % 2 == 0`는  
"n을 받아서 n이 짝수인지(true/false) 반환"이라는 뜻입니다.

## 3) 자주 쓰는 형태
```csharp
Action<string> greet = name => Console.WriteLine($"안녕, {name}");
Func<int, int, int> add = (x, y) => x + y;
```

## 4) 메서드 그룹 변환 (람다 없이 연결)
```csharp
bool IsOdd(int n) => n % 2 == 1;
Func<int, bool> odd = IsOdd;
```

## 5) 초보자 실수 포인트
- `=>` 왼쪽은 입력(매개변수), 오른쪽은 실행식(본문)입니다.
- `Func<...>`에서 **마지막 타입**이 반환형입니다.
- 반환값이 없는 경우는 `Action`을 씁니다.

## 6) 연습 문제
1. 람다식으로 제곱 함수 작성
2. 문자열 리스트에서 길이 3 이상만 출력

---

[상위 문서로 돌아가기](./README.md)
