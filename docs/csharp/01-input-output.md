# C# 입출력

C# 콘솔 프로그램의 기본 흐름은 입력 -> 처리 -> 출력입니다.  
`Console` 클래스를 사용해 빠르게 입문할 수 있습니다.

## 학습 목표
- `Console.WriteLine`, `Console.ReadLine`의 역할을 설명할 수 있다.
- 문자열 입력을 숫자로 변환하는 방법을 적용할 수 있다.
- 입력 변환 시 발생하는 예외 위험을 이해할 수 있다.

## 핵심
- 출력: `Console.WriteLine`, `Console.Write`
- 입력: `Console.ReadLine`
- 변환: `int.Parse` 또는 안전 변환 방식 사용

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

## 실수 포인트
- `ReadLine()` 결과가 `null`일 수 있는데 바로 `Parse`를 호출하는 문제
- 숫자가 아닌 입력에서 `int.Parse` 예외가 발생하는 문제

## 단계별 실습
1. `WriteLine`으로 안내 문구를 출력한다.
2. `ReadLine`으로 문자열을 입력받는다.
3. 정수 입력을 받아 변환 후 결과를 출력한다.

## 이해 점검 체크리스트
- [ ] `WriteLine`과 `Write` 차이를 설명할 수 있는가?
- [ ] `ReadLine` 입력을 숫자로 변환할 수 있는가?
- [ ] 변환 실패 가능성을 고려해 검증 로직을 생각했는가?

## 4) 연습 문제
1. 이름/나이를 입력받아 자기소개를 출력하세요.
2. 정수 2개를 입력받아 합계를 출력하세요.

## 셀프 퀴즈
1. `Console.ReadLine()`의 반환 타입은 무엇인가?
2. `int.Parse` 사용 시 주의해야 할 대표 위험은?

## 정답
1. `string`
2. 숫자가 아닌 입력에서 예외가 발생할 수 있음

## 관련 브릿지 학습
- [디버깅 워크플로우 기초](../common/01-debugging-workflow.md)
- [버전 관리와 협업 기초](../common/02-version-control-collaboration.md)

---

[상위 문서로 돌아가기](./README.md)
