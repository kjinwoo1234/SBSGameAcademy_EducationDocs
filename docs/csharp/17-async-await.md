# C# 비동기 프로그래밍

## 핵심
- `async`/`await`로 오래 걸리는 작업을 비차단으로 처리
- 반환 타입은 `Task`, `Task<T>`

## 예제
```csharp
static async Task WaitAsync()
{
    await Task.Delay(1000);
    Console.WriteLine("완료");
}
```

---

[상위 문서로 돌아가기](./README.md)
