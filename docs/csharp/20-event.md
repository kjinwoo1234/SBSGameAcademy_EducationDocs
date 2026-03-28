# C# 이벤트

## 핵심
- 특정 사건 발생 시 등록된 핸들러 실행
- `event` 키워드로 외부 호출을 제한해 안전성 확보

## 예제
```csharp
class Alarm
{
    public event Action OnAlarm;
    public void Start() => OnAlarm?.Invoke();
}
```

---

[상위 문서로 돌아가기](./README.md)
