# Chapter 20 메모리 관리

## 학습 목표
- 값 타입과 참조 타입이 대입될 때 복사·공유 차이를 구분한다.
- GC가 관리 메모리를 회수한다는 점을 설명할 수 있다.
- `IDisposable`과 `using`으로 파일 자원을 정리할 수 있다.

## 본문

### 20-1 스택과 힙, 값과 참조

**스택**과 **힙**은 프로그램이 값을 두는 영역을 나누어 부르는 말입니다. 입문에서는 이렇게 기억하면 됩니다. **값 타입**은 변수에 값 자체가 들어가고, 대입하면 **복사**됩니다. **참조 타입**은 변수에 **참조**가 들어가고, 대입하면 **같은 객체를 함께** 가리킵니다.

`int` 같은 기본 숫자 타입이 값 타입입니다. **`struct`**로 만든 타입도 값 타입입니다. **`class`**로 만든 인스턴스는 참조 타입이며, 객체 본체는 힙에 둡니다. 참조 변수는 그 객체를 가리키는 화살표처럼 동작합니다.

아래를 실행해 `Point`는 복사본만 바뀌고, `Player`는 한쪽을 바꿔도 다른 쪽 출력에 반영되는지 확인해 보세요.

```csharp
using System;

struct Point
{
    public int X;
}

class Player
{
    public int Hp;
}

class Program
{
    static void Main()
    {
        Point a = new Point();
        a.X = 10;
        Point b = a;
        b.X = 99;
        Console.WriteLine(a.X);
        Console.WriteLine(b.X);

        Player p1 = new Player();
        p1.Hp = 100;
        Player p2 = p1;
        p2.Hp = 50;
        Console.WriteLine(p1.Hp);
        Console.WriteLine(p2.Hp);
    }
}
```

**예상 출력**

```text
10
99
50
50
```

**코드 해석**
- `Point`는 `struct`라서 `b = a`는 값 복사입니다. `b.X`만 바꿔도 `a.X`는 `10`입니다.
- `Player`는 `class`라서 `p2 = p1`은 같은 객체를 가리킵니다. `p2.Hp`를 바꾸면 `p1.Hp`도 `50`입니다.

| 구분 | 대표 | 대입 시 |
|---|---|---|
| 값 타입 | `int`, `struct` | 값 복사 |
| 참조 타입 | `class` 인스턴스 | 참조 복사. 같은 대상 |

### 20-2 GC

**GC**는 가비지 컬렉터입니다. 더 이상 어떤 변수도 가리키지 않는 **관리 힙 객체** 메모리를 자동으로 회수합니다. 회수 **시점**은 프로그래머가 줄마다 정하지 않습니다.

파일 핸들처럼 OS가 맡는 **비관리 자원**은 GC만으로 바로·확실히 닫힌다고 보장하기 어렵습니다. 그런 자원은 다음 소절의 `Dispose`/`using`으로 정리합니다.

| 수단 | 대상 | 역할 |
|---|---|---|
| GC | 관리 메모리. 객체 | 자동 회수. 시점은 비보장 |
| `using` / `Dispose` | 파일·스트림 등 | 사용이 끝나면 바로 정리 |

### 20-3 IDisposable과 using

**`IDisposable`**은 `Dispose()`로 **명시적 정리**가 필요한 자원을 위한 인터페이스입니다. 인터페이스는 13장에서 본 “계약”과 같습니다. **`using (자원) { ... }`** 블록이 끝나면 `Dispose()`가 자동 호출됩니다.

**`StreamWriter`**는 파일에 텍스트를 **한 줄씩 쓰는** 객체입니다. 15장의 `File.WriteAllText`가 한 번에 통째로 쓰는 방식과 달리, 쓰는 동안 파일 핸들을 열어 둡니다. 그래서 `using`으로 감싸 닫기를 보장합니다.

| 용어 | 의미 |
|---|---|
| IDisposable | `Dispose()`로 정리하는 계약 |
| 비관리 자원 | 런타임이 자동으로 바로 닫아 주지 않는 OS 자원 |

아래를 실행해 `saved`가 출력되고, 실행 폴더에 `log.txt`가 생기는지 확인해 보세요.

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
- `using` 안에서 `StreamWriter`로 `log.txt`에 `hello`를 씁니다.
- 블록이 끝나면 `Dispose()`가 호출되어 파일 핸들을 정리합니다.
- 그다음 `saved`를 출력합니다.

이 장에서는 값/참조 대입 차이, GC의 역할, `using`으로 파일 정리까지입니다. 세대·파이널라이저 같은 심화는 다루지 않습니다.

### 연습문제

**문제 1**
- 문제: `log.txt`에 `hello`가 있다고 가정하고, `StreamReader`로 첫 줄을 읽으세요.
- 입력: 없음. 본문 예제로 `log.txt`를 만들어 둔 상태
- 출력:
  ```text
  hello
  ```
- 조건: `using (StreamReader ...)` 안에서 `ReadLine()` 사용

**문제 2**
- 문제: `struct` 복사와 `class` 참조 공유를 숫자 출력으로 보이세요.
- 입력: 없음
- 출력:
  ```text
  10
  99
  50
  50
  ```
- 조건: `struct` 필드 하나, `class` 필드 하나. 대입 후 복사본/다른 참조만 값을 바꾼 뒤 네 줄 출력

**문제 3**
- 문제: GC와 `using`/`Dispose` 중 “파일 핸들을 사용 직후 닫을 때” 맞는 쪽을 고르고 이유를 한 줄로 쓰세요.
- 입력: 없음
- 출력: `using` 또는 `Dispose` 와 “파일 등 비관리 자원은 즉시 정리” 취지의 한 줄
- 조건: 본문 표 기준. GC만으로 즉시 닫힘을 보장한다고 쓰지 말 것

### 정답 포인트

- 문제 1: `using (StreamReader sr = new StreamReader("log.txt")) { Console.WriteLine(sr.ReadLine()); }`
- 문제 2: `struct`는 복사본 변경이 원본과 무관. `class`는 같은 객체라 둘 다 `50`
- 문제 3: `using`/`Dispose`. GC는 관리 메모리 회수이고 시점도 비보장
- 공통: 값/참조 대입 차이를 출력으로 확인하고, 파일은 `using`으로 정리

---

[상위 문서로 돌아가기](./README.md)
