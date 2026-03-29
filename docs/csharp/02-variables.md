# C# 변수와 자료형

변수는 데이터를 저장하는 이름입니다.  
자료형은 저장 가능한 값의 종류와 연산 규칙을 정의합니다.

## 학습 목표
- C# 주요 자료형을 구분하고 목적에 맞게 사용할 수 있다.
- 문자열 입력을 숫자로 안전하게 변환할 수 있다.
- `Parse`와 `TryParse` 차이를 설명할 수 있다.

## 핵심
- 기본형: `int`, `float`, `double`, `bool`
- 문자열: `string`
- 변환: `Parse` / `TryParse`

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

## 실수 포인트
- `Parse`를 바로 호출해 잘못된 입력에서 예외가 발생한다.
- `float`/`double` 사용 목적을 구분하지 못한다.
- 입력 검증 없이 계산 로직을 진행한다.

## 단계별 실습
1. `int`, `double`, `string`, `bool` 변수를 선언하고 출력한다.
2. `ReadLine` 입력을 `int.Parse`로 변환해 출력한다.
3. 같은 입력을 `TryParse`로 처리해 실패 분기까지 구현한다.

## 이해 점검 체크리스트
- 자료형별 저장 값 특성을 설명할 수 있는가?
- `Parse`와 `TryParse` 차이를 설명할 수 있는가?
- 입력 실패 시 대체 처리 코드를 작성할 수 있는가?

## 4) 연습 문제
1. 문자열 입력을 `int`로 변환 후 제곱값 출력
2. `TryParse`로 잘못된 입력을 처리하는 코드 작성

## 셀프 퀴즈
1. 사용자 입력이 숫자가 아닐 수 있을 때 권장되는 변환 방식은?
2. `int.Parse("abc")`는 어떤 문제가 발생할 수 있는가?

## 정답
1. `int.TryParse`
2. 변환 예외가 발생해 프로그램이 중단될 수 있다

---

[상위 문서로 돌아가기](./README.md)
