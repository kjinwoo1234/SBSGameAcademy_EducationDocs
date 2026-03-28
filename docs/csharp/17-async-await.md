# C# 비동기 프로그래밍

비동기는 "오래 걸리는 작업을 기다리는 동안 프로그램이 멈추지 않게" 해주는 방식입니다.  
초보자 기준으로는 **다운로드/파일읽기/DB조회 같은 대기 작업을 부드럽게 처리하는 문법**입니다.

## 1) 핵심 키워드
- `async`: 비동기 메서드 선언
- `await`: 비동기 작업 완료까지 비차단 대기
- `Task`, `Task<T>`: 비동기 결과 타입

## 2) 동기 vs 비동기 감각 잡기
- 동기: 작업 끝날 때까지 다음 코드가 멈춤
- 비동기: 기다리는 동안 다른 코드를 진행할 수 있음

## 3) 기본 예제
```csharp
using System.Threading.Tasks;

static async Task WaitAsync()
{
    Console.WriteLine("시작");
    await Task.Delay(1000);
    Console.WriteLine("완료");
}
```

## 4) 값 반환 비동기
```csharp
static async Task<int> GetValueAsync()
{
    await Task.Delay(500);
    return 42;
}
```

호출할 때는 보통 `await GetValueAsync();` 형태로 사용합니다.

## 5) 초보자 실수 포인트
- `Task.Result`/`Wait()` 남용 시 교착 위험
- 가능하면 끝까지 `await` 체인 유지
- `async void`는 이벤트 핸들러 외에는 피하기

## 6) 연습 문제
1. 1초 대기 후 메시지 출력 비동기 메서드 작성
2. 두 Task를 병렬 실행해 완료 순서 확인

---

[상위 문서로 돌아가기](./README.md)
