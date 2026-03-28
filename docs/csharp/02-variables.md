# C# 변수와 자료형

변수는 데이터를 저장하는 이름입니다.  
자료형은 저장 가능한 값의 종류와 연산 규칙을 정의합니다.

## 1) 자주 쓰는 자료형
- `int`: 정수
- `float`, `double`: 실수
- `string`: 문자열
- `bool`: 참/거짓

```csharp
int age = 20;
double height = 175.5;
string name = "홍길동";
bool isStudent = true;
```

## 2) 형 변환
```csharp
string input = "123";
int n = int.Parse(input);
double d = Convert.ToDouble("3.14");
```

## 3) 안전한 변환 (`TryParse`)
```csharp
if (int.TryParse("abc", out int value))
{
    Console.WriteLine(value);
}
else
{
    Console.WriteLine("숫자 변환 실패");
}
```

## 4) 연습 문제
1. 문자열 입력을 `int`로 변환 후 제곱값 출력
2. `TryParse`로 잘못된 입력을 처리하는 코드 작성

---

[상위 문서로 돌아가기](./README.md)
