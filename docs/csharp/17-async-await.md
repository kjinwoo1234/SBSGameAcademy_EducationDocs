# C# 비동기 프로그래밍

비동기 프로그래밍은 오래 걸리는 작업 중에도 앱 응답성을 유지하게 해줍니다.  
UI 앱, 네트워크, 파일 처리에서 필수적으로 사용됩니다.

## 1) 핵심 키워드
- `async`: 비동기 메서드 선언
- `await`: 비동기 작업 완료까지 비차단 대기
- `Task`, `Task<T>`: 비동기 결과 타입

## 2) 기본 예제
```csharp
using System.Threading.Tasks;

static async Task WaitAsync()
{
    Console.WriteLine("시작");
    await Task.Delay(1000);
    Console.WriteLine("완료");
}
```

## 3) 값 반환 비동기
```csharp
static async Task<int> GetValueAsync()
{
    await Task.Delay(500);
    return 42;
}
```

## 4) 주의 사항
- `Task.Result`/`Wait()` 남용 시 교착 위험
- 가능하면 끝까지 `await` 체인 유지

## 5) 연습 문제
1. 1초 대기 후 메시지 출력 비동기 메서드 작성
2. 두 Task를 병렬 실행해 완료 순서 확인

---

[상위 문서로 돌아가기](./README.md)
