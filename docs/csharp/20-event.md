# C# 이벤트

이벤트는 "어떤 일이 발생했음을 구독자에게 알리는 기능"입니다.  
초보자 관점에서는 **한 객체가 신호를 보내고, 다른 객체들이 반응하는 구조**라고 보면 됩니다.

## 1) 기본 이벤트 선언/발행
```csharp
class Alarm
{
    public event Action OnAlarm;

    public void Start()
    {
        Console.WriteLine("알람 시작");
        OnAlarm?.Invoke();
    }
}
```

## 2) 구독/해제
```csharp
Alarm alarm = new Alarm();
alarm.OnAlarm += () => Console.WriteLine("구독자 A");
alarm.Start();

// 필요 시 해제
// alarm.OnAlarm -= SomeHandler;
```

## 3) `event`를 쓰는 이유
- 외부에서 직접 `Invoke`를 막아 안전성 강화
- 발행자/구독자 역할 분리

## 4) 동작 흐름 한 줄 요약
1. 구독자가 이벤트에 함수 등록(`+=`)  
2. 발행자가 특정 시점에 이벤트 발생(`Invoke`)  
3. 등록된 함수들이 순서대로 실행

## 5) 초보자 실수 포인트
- 이벤트를 구독만 하고 해제하지 않아 메모리 누수 위험
- `event` 없이 delegate 필드를 public으로 열어 안전성 저하

## 6) 연습 문제
1. 점수 변경 이벤트를 발행하는 클래스 작성
2. 이벤트 구독자 2개를 등록해 동작 확인

---

[상위 문서로 돌아가기](./README.md)
