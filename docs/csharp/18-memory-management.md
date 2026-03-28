# C# 메모리 관리

C#은 GC(가비지 컬렉터) 기반 자동 메모리 관리를 제공합니다.  
개발자는 수동 해제보다 객체 수명과 자원 해제 시점을 설계하는 데 집중해야 합니다.

## 1) 값 타입 vs 참조 타입
- 값 타입: 스택에 값 저장 (`int`, `struct` 등)
- 참조 타입: 힙 객체를 참조 (`class`, `string`, 배열 등)

```csharp
int a = 10;
string b = "홍길동";
```

## 2) GC 개념
- 참조가 끊긴 객체를 수거
- 즉시 수거가 보장되지는 않음

## 3) `IDisposable`과 `using`
파일/소켓/DB 연결 같은 unmanaged 자원은 명시적 해제가 필요합니다.

```csharp
using (var reader = new StreamReader("test.txt"))
{
    Console.WriteLine(reader.ReadLine());
}
```

## 4) 연습 문제
1. `using` 블록으로 파일 읽기 구현
2. 값/참조 복사 차이를 코드로 비교

---

[상위 문서로 돌아가기](./README.md)
