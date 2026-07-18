# Chapter 20 메모리 관리

## 학습 목표
- C# 메모리 관리 모델(스택/힙/GC)을 이해한다.
- IDisposable과 `using`의 필요성을 설명할 수 있다.

## 본문

### 20-1 스택과 힙

대략적으로 **값 타입**(`int`, `struct` 등) 값은 **스택**에 두고, **참조 타입**(`class` 인스턴스) 본체는 **힙**에 둡니다. 참조 변수 자체는 스택에 있고, 화살표처럼 힙 객체를 가리킵니다. `struct`를 변수에 대입하면 **복사**, `class`는 **같은 객체 참조**를 나누는 차이가 납니다.

| 구분 | 주로 어디에 | 대입 시 |
|---|---|---|
| 값 타입 | 스택(개념적으로) | 값 복사 |
| 참조 타입 | 힙에 객체 | 참조 복사(같은 대상) |

### 20-2 GC

**GC**(Garbage Collector, 가비지 컬렉터)는 더 이상 참조되지 않는 **관리 힙 객체** 메모리를 자동 회수합니다. 다만 **파일 핸들·스트림** 같은 **비관리 자원**은 GC만으로 즉시·확실히 닫히지 않을 수 있습니다.

### 20-3 IDisposable과 using

**`IDisposable`**은 `Dispose()`로 **명시적 정리**가 필요한 자원을 위한 계약입니다. **`using (...) { }`** 블록이 끝나면 `Dispose()`가 자동 호출되어 닫기를 잊기 어렵게 합니다.

| 수단 | 대상 | 역할 |
|---|---|---|
| GC | 관리 메모리(객체) | 자동 회수(시점 비보장) |
| `using` / `Dispose` | 파일·스트림 등 | 사용 끝 즉시 정리 |

| 용어 | 의미 |
|---|---|
| IDisposable | 수동 정리가 필요한 자원을 해제하기 위한 인터페이스 |
| 비관리 자원(unmanaged resource) | 런타임이 자동 관리하지 않는 OS 자원(파일 핸들 등) |

아래 코드를 실행해 `saved`가 출력되고, 실행 폴더에 `log.txt`가 생기는지 확인해 보세요.

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

**예상 출력**

```text
saved
```

**코드 해석**
- `using` 안에서 `StreamWriter`로 파일에 한 줄을 씁니다.
- 블록이 끝나면 `Dispose()`가 호출되어 파일 핸들을 정리합니다.
- 그 다음 `saved`를 출력합니다.

### 연습문제

**문제 1**
- 문제: 파일 읽기를 `using`으로 감싸 자원 해제를 보장하세요.
- 입력: 없음(먼저 `log.txt` 등에 `hello` 저장했다고 가정)
- 출력: `hello` (파일 첫 줄 또는 내용)
- 조건: `using (StreamReader ...)` 또는 동등 구조

**문제 2**
- 문제: `struct`와 `class`의 대입 차이를 출력으로 보이세요.
- 입력: 없음
- 출력: 예: struct 복사 후 원본이 그대로, class는 한쪽 변경이 다른 쪽에도 보임 — 숫자 두 줄 이상으로 확인
- 조건: 같은 필드에 값을 바꾼 뒤 원본/복사본(또는 두 참조)을 출력

### 정답 포인트

- 문제 1: `using (var sr = new StreamReader(path)) { Console.WriteLine(sr.ReadLine()); }`
- 문제 2: struct는 복사본 변경이 원본과 무관, class는 공유 객체
- 공통: GC는 메모리, 파일 등은 `using`/`Dispose`

---

[상위 문서로 돌아가기](./README.md)
