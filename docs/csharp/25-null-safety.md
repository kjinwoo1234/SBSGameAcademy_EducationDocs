# Chapter 25 Null 안전성

## 학습 목표
- `null` 관련 런타임 오류가 왜 발생하는지 이해한다.
- nullable reference types와 null 안전 연산자를 적용할 수 있다.

## 본문

### 25-1 null과 예외

참조형 변수가 **`null`**(가리키는 객체 없음)인데 `.`으로 멤버에 접근하면 **`NullReferenceException`**이 납니다. **null 안전성**은 이런 실수를 실행 전에 드러내고, 실행 시에는 기본값·조기 반환으로 막는 습관입니다.

### 25-2 nullable reference types

**NRT**(Nullable Reference Types)를 켜면(`#nullable enable`) `string`은 null 불가, **`string?`**는 null 가능으로 **의도**를 타입에 적습니다. 컴파일러가 null 위험 코드에 경고를 줍니다.

### 25-3 null 안전 연산자

**`?.`**는 왼쪽이 null이면 멤버 접근을 하지 않고 null을 돌립니다. **`??`**는 왼쪽이 null일 때 오른쪽 기본값을 씁니다. **`??=`**는 왼쪽이 null일 때만 대입합니다.

| 표현 | 의미 |
|---|---|
| `?.` | null이면 접근 중단 후 null 반환 |
| `??` | 좌변이 null일 때 우변 기본값 사용 |
| `??=` | 좌변이 null일 때만 대입 |

아래 코드를 실행해 `user`가 null이어도 `Guest`가 출력되는지 확인해 보세요.

```csharp
#nullable enable
using System;

class User
{
    public string? Nickname { get; set; }
}

class Program
{
    static void Main()
    {
        User? user = null;
        string displayName = user?.Nickname ?? "Guest";
        Console.WriteLine(displayName);

        string? path = null;
        path ??= "C:\\data";
        Console.WriteLine(path);
    }
}
```

**예상 출력**

```text
Guest
C:\data
```

**코드 해석**
- `user?.Nickname`은 `user`가 null이라 예외 없이 null입니다.
- `?? "Guest"`가 기본값을 채웁니다.
- `path ??= "C:\\data"`는 null일 때만 경로를 넣습니다.

### 연습문제

**문제 1**
- 문제: 프로필이 null이어도 예외 없이 이름을 출력하세요.
- 입력: 없음(`User?`가 null)
- 출력: `Guest`
- 조건: `?.`, `??` 사용

**문제 2**
- 문제: 설정 문자열이 null이면 기본 경로를 할당하세요.
- 입력: 없음(`string? path = null`)
- 출력: `/tmp` (또는 정한 기본 경로)
- 조건: `??=` 사용

**문제 3**
- 문제: `#nullable enable`에서 경고가 나는 코드를 고치세요.
- 입력: 없음(예: `string s = null;` 형태)
- 출력: 경고 없이 컴파일되는 코드 + 원인 1줄(예: `string`에 null 대입)
- 조건: `string?`로 바꾸거나 null 검사 후 대입

### 정답 포인트

- 문제 1: `user?.Name ?? "Guest"`
- 문제 2: `path ??= "/tmp";`
- 문제 3: null 가능이면 `string?`, 아니면 대입 전 검사
- 공통: NRT로 의도 명시 + 안전 연산자로 실행 시 방어

---

[상위 문서로 돌아가기](./README.md)
