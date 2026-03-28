# C# 델리게이트

델리게이트는 메서드 참조를 객체처럼 다루는 타입입니다.  
콜백, 이벤트, 전략 패턴 구현의 기반이 됩니다.

## 1) 사용자 정의 델리게이트
```csharp
delegate void MessageHandler(string msg);

static void PrintMsg(string msg) => Console.WriteLine(msg);
MessageHandler handler = PrintMsg;
handler("안녕하세요");
```

## 2) 표준 델리게이트
- `Action`: 반환값 없음
- `Func`: 반환값 있음
- `Predicate<T>`: bool 반환

```csharp
Action<string> show = msg => Console.WriteLine(msg);
Func<int, int, int> add = (x, y) => x + y;
```

## 3) 연습 문제
1. 문자열 출력 콜백 메서드 전달
2. `Func`로 계산기 연산 함수를 구성

---

[상위 문서로 돌아가기](./README.md)
