# C# 연산자

연산자는 계산과 조건 평가를 담당합니다.  
정확한 조건식 작성은 버그 예방의 핵심입니다.

## 1) 산술/증감 연산자
```csharp
int a = 10, b = 3;
Console.WriteLine(a + b);
Console.WriteLine(a % b);

int n = 5;
Console.WriteLine(++n); // 6
Console.WriteLine(n++); // 6
```

## 2) 비교/논리 연산자
```csharp
int score = 85;
bool isB = (score >= 80) && (score < 90);
bool isPass = score >= 60 || score == 50;
```

## 3) 복합 대입 연산자
```csharp
int money = 1000;
money += 500;
money -= 200;
```

## 4) Null 관련 연산자
- `??`: null이면 대체값 사용
- `?.`: null 안전 접근

```csharp
string name = null;
Console.WriteLine(name ?? "기본값");
Console.WriteLine(name?.Length);
```

## 5) 연습 문제
1. 점수 입력 후 A/B/C 분류 불린식 작성
2. `??`를 사용해 null 문자열 처리

---

[상위 문서로 돌아가기](./README.md)
