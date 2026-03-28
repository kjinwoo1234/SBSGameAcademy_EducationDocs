# C# 제어문

제어문은 프로그램 실행 경로를 결정합니다.  
조건 분기와 반복을 명확히 설계하면 코드 가독성이 크게 향상됩니다.

## 1) 조건문
```csharp
int score = 87;
if (score >= 90)
    Console.WriteLine("A");
else if (score >= 80)
    Console.WriteLine("B");
else
    Console.WriteLine("C");
```

## 2) `switch`
```csharp
int menu = 2;
switch (menu)
{
    case 1: Console.WriteLine("조회"); break;
    case 2: Console.WriteLine("등록"); break;
    default: Console.WriteLine("기타"); break;
}
```

## 3) 반복문과 흐름 제어
```csharp
for (int i = 1; i <= 10; i++)
{
    if (i == 3) continue;
    if (i == 8) break;
    Console.WriteLine(i);
}
```

## 4) 연습 문제
1. 1~100 중 3의 배수 합계 계산
2. 메뉴 입력에 따라 분기하는 `switch` 작성

---

[상위 문서로 돌아가기](./README.md)
