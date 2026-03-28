# C# 이벤트

이벤트는 "상태 변화 알림"을 안전하게 구현하는 메커니즘입니다.  
UI 클릭, 게임 상태 변화, 서버 수신 알림 등에서 사용됩니다.

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
```

## 3) `event`를 쓰는 이유
- 외부에서 직접 `Invoke`를 막아 안전성 강화
- 발행자/구독자 역할 분리

## 4) 연습 문제
1. 점수 변경 이벤트를 발행하는 클래스 작성
2. 이벤트 구독자 2개를 등록해 동작 확인

---

[상위 문서로 돌아가기](./README.md)
