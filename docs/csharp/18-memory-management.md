# Chapter 18 메모리 관리

## 학습 목표
- C# 메모리 관리 모델(스택/힙/GC)을 이해한다.
- IDisposable과 `using`의 필요성을 설명할 수 있다.

## 세부 주제
### 18-1 스택과 힙
- 값 형식/참조 형식

### 18-2 GC
- 가비지 컬렉션 동작 개요

### 18-3 IDisposable과 using
- 비관리 자원 정리

## 실습 체크리스트
- 파일/스트림 자원을 `using`으로 안전하게 해제한다.

## 본문

### 18-1 스택과 힙
- 값 형식은 주로 스택, 참조 형식 인스턴스는 힙에 저장됩니다.
- 참조 변수 자체는 스택에 있고, 객체 본체는 힙에 있는 경우가 많습니다.

### 18-2 GC
- GC(Garbage Collector)는 더 이상 참조되지 않는 객체 메모리를 자동 회수합니다.
- 하지만 파일 핸들 같은 비관리 자원은 GC만으로 충분하지 않을 수 있습니다.

### 18-3 IDisposable과 using
- `IDisposable`은 명시적 정리가 필요한 자원을 다루는 인터페이스입니다.
- `using` 블록을 쓰면 블록 종료 시 `Dispose()`가 자동 호출됩니다.

약어 설명(GC):
- **GC**는 **Garbage Collector**의 약자입니다.
- 한국어로는 "가비지 컬렉터"이며, 사용되지 않는 메모리를 자동 회수하는 런타임 기능입니다.
- 메모리 누수를 줄여주지만, 자원 해제 시점을 항상 즉시 보장하는 도구는 아닙니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| IDisposable | 수동 정리가 필요한 자원을 해제하기 위한 인터페이스 |
| 비관리 자원(unmanaged resource) | 런타임이 자동 관리하지 않는 OS 자원(파일 핸들 등) |

예시 코드:
```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        using (StreamWriter sw = new StreamWriter("log.txt"))
        {
            sw.WriteLine("hello");
        }
        Console.WriteLine("saved");
    }
}
```

예상 출력:
```text
saved
```

코드 해석:
- `using` 블록 안에서 `StreamWriter`를 생성해 파일에 기록합니다.
- 블록이 끝나면 `Dispose()`가 자동 호출되어 자원 누수를 줄입니다.

연습문제:
1. 문제 1 - using 적용
   - 문제: 파일 읽기 코드를 `using`으로 감싸 자원 해제를 보장하세요.
   - 입력: 파일 경로
   - 출력: 파일 내용
   - 조건: `Dispose` 자동 호출 구조
2. 문제 2 - 값/참조 타입 구분
   - 문제: `struct`와 `class` 예제를 만들어 저장 위치 차이를 설명하세요.
   - 입력: 없음
   - 출력: 설명 문장/예제 코드
   - 힌트: 복사 동작 비교

정답 포인트:
- C#도 메모리/자원 관리 개념을 알아야 안정적 코드 작성 가능
- 비관리 자원은 `using` 또는 명시적 `Dispose`가 핵심

---

[상위 문서로 돌아가기](./README.md)
