# C# 델리게이트

델리게이트는 "함수 자체를 변수에 담아 전달"하는 기능입니다.  
쉽게 말해, **나중에 실행할 함수를 미리 저장해두는 상자**입니다.

## 1) 사용자 정의 델리게이트
```csharp
delegate void MessageHandler(string msg);

static void PrintMsg(string msg) => Console.WriteLine(msg);
MessageHandler handler = PrintMsg;
handler("안녕하세요");
```

## 2) 표준 델리게이트 (실무에서 더 자주 사용)
- `Action`: 반환값 없음
- `Func`: 반환값 있음
- `Predicate<T>`: bool 반환

```csharp
Action<string> show = msg => Console.WriteLine(msg);
Func<int, int, int> add = (x, y) => x + y;
```

## 3) 언제 쓰나?
- 콜백 함수 전달
- 이벤트 처리 기반 구성
- 조건/전략 함수를 외부에서 주입

## 4) 초보자 실수 포인트
- `Action`과 `Func`의 차이 혼동
- 매개변수 순서와 반환형 위치 혼동 (`Func` 마지막 타입이 반환형)

## 5) 연습 문제
1. 문자열 출력 콜백 메서드 전달
2. `Func`로 계산기 연산 함수를 구성

---

[상위 문서로 돌아가기](./README.md)
