# C# 입출력

C# 콘솔 프로그램의 기본 흐름은 입력 -> 처리 -> 출력입니다.  
`Console` 클래스를 사용해 빠르게 입문할 수 있습니다.

## 1) 출력
- `Console.WriteLine`: 출력 후 줄바꿈
- `Console.Write`: 줄바꿈 없이 출력

```csharp
Console.WriteLine("안녕하세요");
Console.Write("점수: ");
Console.Write(100);
```

## 2) 입력
`Console.ReadLine()`은 문자열을 반환하므로 필요 시 형 변환이 필요합니다.

```csharp
Console.Write("나이 입력: ");
string input = Console.ReadLine();
int age = int.Parse(input);
Console.WriteLine($"입력 나이: {age}");
```

## 3) 문자열 보간
```csharp
string name = "홍길동";
int score = 95;
Console.WriteLine($"{name}님의 점수는 {score}점입니다.");
```

## 4) 연습 문제
1. 이름/나이를 입력받아 자기소개를 출력하세요.
2. 정수 2개를 입력받아 합계를 출력하세요.

---

[상위 문서로 돌아가기](./README.md)
