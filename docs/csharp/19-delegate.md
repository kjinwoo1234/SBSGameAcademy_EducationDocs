# Chapter 19 delegate

## 학습 목표
- 델리게이트의 개념과 사용 목적을 이해한다.
- 메서드를 변수처럼 전달/저장하는 방식을 익힌다.

## 세부 주제
### 19-1 delegate 기본
- 메서드 시그니처 연결

### 19-2 다중 호출
- 멀티캐스트 델리게이트

### 19-3 `Action`/`Func`
- 내장 델리게이트 타입

## 실습 체크리스트
- 공격 로직을 델리게이트로 교체 가능한 구조로 만든다.

## 본문

### 19-1 delegate 기본
- 델리게이트는 "메서드를 가리키는 타입"입니다.
- 같은 시그니처를 가진 메서드를 런타임에 바꿔 끼울 수 있습니다.

### 19-2 다중 호출
- 델리게이트는 여러 메서드를 체인으로 묶어 순차 호출할 수 있습니다.
- 구독 해지는 `-=` 연산으로 처리합니다.

### 19-3 `Action`/`Func`
- `Action`은 반환값 없는 델리게이트, `Func`는 반환값 있는 델리게이트입니다.
- 직접 delegate 타입을 정의하기 전에 `Action`/`Func`를 먼저 고려하면 편합니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 시그니처(signature) | 메서드를 구분하는 정보(매개변수/반환형) |
| 멀티캐스트 | 한 델리게이트에 여러 메서드를 연결해 순차 실행하는 방식 |

예시 코드:
```csharp
using System;

delegate void LogHandler(string msg);

class Program
{
    static void ConsoleLog(string msg) => Console.WriteLine(msg);

    static void Main()
    {
        LogHandler handler = ConsoleLog;
        handler("delegate test");
    }
}
```

예상 출력:
```text
delegate test
```

코드 해석:
- `LogHandler` 타입은 `string` 인자를 받는 메서드만 연결할 수 있습니다.
- `handler = ConsoleLog`로 메서드를 변수처럼 저장한 뒤 호출합니다.

연습문제:
1. 문제 1 - Func 계산기
   - 문제: `Func<int, int, int>`로 덧셈/곱셈 함수를 바꿔 실행하세요.
   - 입력: 정수 2개
   - 출력: 연산 결과
   - 조건: 델리게이트 변수 재할당
2. 문제 2 - 멀티캐스트 로그
   - 문제: 로그 메서드 2개를 연결해 한 번에 호출하세요.
   - 입력: 로그 문자열
   - 출력: 로그 2줄
   - 힌트: `+=` 연산 사용

정답 포인트:
- 델리게이트는 전략 교체/콜백 구현에 유용
- 시그니처가 맞아야 연결 가능

---

[상위 문서로 돌아가기](./README.md)
