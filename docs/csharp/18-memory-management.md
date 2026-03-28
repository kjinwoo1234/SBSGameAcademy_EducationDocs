# C# 메모리 관리

C#은 메모리를 자동으로 관리하는 언어입니다.  
하지만 "자동"이라고 해서 아무것도 몰라도 되는 것은 아니고, 값/참조 차이와 `using`은 꼭 알아야 합니다.

## 1) 값 타입 vs 참조 타입 (아주 쉽게)
- 값 타입: 변수 안에 실제 값이 직접 들어감 (`int`, `double`, `struct`)
- 참조 타입: 변수 안에는 "객체 주소"가 들어감 (`class`, `string`, 배열)

```csharp
int a = 10;              // 값 타입
string b = "홍길동";      // 참조 타입
```

## 2) 복사 동작 차이
```csharp
int x = 10;
int y = x;   // 값 복사
y = 20;
Console.WriteLine(x); // 10 (영향 없음)
```

```csharp
class User { public string Name; }
User u1 = new User { Name = "홍길동" };
User u2 = u1; // 주소 복사 (같은 객체를 가리킴)
u2.Name = "이순신";
Console.WriteLine(u1.Name); // 이순신 (영향 있음)
```

## 3) GC(가비지 컬렉션) 개념
- 더 이상 참조되지 않는 객체를 GC가 정리
- "언제 즉시 정리될지"는 보장되지 않음
- 따라서 파일/소켓 같은 자원은 GC만 믿으면 안 됨

## 4) `IDisposable`과 `using` (매우 중요)
파일/소켓/DB 연결은 사용 후 즉시 닫아야 합니다.  
`using`을 쓰면 블록 종료 시점에 자동으로 `Dispose()`가 호출됩니다.

```csharp
using (var reader = new StreamReader("test.txt"))
{
    Console.WriteLine(reader.ReadLine());
}
```

## 5) 초보자 실수 포인트
- 참조 타입을 값 타입처럼 생각하고 수정 부작용을 놓침
- `using` 없이 파일을 열어두고 잠금 문제를 만듦
- GC가 "바로 정리해주겠지"라고 오해함

## 6) 연습 문제
1. `using` 블록으로 파일 읽기 구현
2. 값/참조 복사 차이를 코드로 비교

---

[상위 문서로 돌아가기](./README.md)
