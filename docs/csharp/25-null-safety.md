# Chapter 25 Null 안전성

## 학습 목표
- 참조가 `null`일 때 `.` 접근이 `NullReferenceException`을 내는 이유를 설명한다.
- `#nullable enable`에서 `string`과 `string?` 의도를 구분한다.
- `?.`, `??`, `??=`로 null일 때 안전하게 읽고 기본값을 넣을 수 있다.

## 본문

### 25-1 null과 예외

참조형 변수는 객체를 가리키거나, 아무 것도 가리키지 않는 **`null`**일 수 있습니다. `null`인 변수에 `.`으로 멤버를 쓰면 실행 중 **`NullReferenceException`**이 납니다. **null 안전성**은 이런 실수를 컴파일 경고로 드러내고, 실행 시에는 기본값·조기 반환으로 막는 습관입니다.

아래는 일부러 위험한 형태입니다. 주석 처리된 줄을 켜면 예외가 납니다. 지금은 주석으로만 알아 두고, 왜 위험한지 읽어 보세요.

```csharp
using System;

class User
{
    public string Nickname { get; set; } = "";
}

class Program
{
    static void Main()
    {
        User? user = null;
        // Console.WriteLine(user.Nickname); // null인데 . 접근 → 예외
        Console.WriteLine("safe");
    }
}
```

**예상 출력**

```text
safe
```

**코드 해석**
- `User? user = null;`은 아직 객체가 없습니다.
- `user.Nickname`처럼 `.`으로 들어가면 `NullReferenceException`이 납니다.
- 주석을 유지한 채 `"safe"`만 출력됩니다.

### 25-2 nullable reference types

**Nullable Reference Types**를 켜면 참조형에도 null 가능 여부를 타입에 적을 수 있습니다. 파일 위나 프로젝트에 `#nullable enable`을 두면, `string`은 null을 넣지 않겠다는 뜻이고 **`string?`**는 null일 수 있다는 뜻입니다. 컴파일러는 null 위험이 보이는 코드에 **경고**를 줍니다. 경고는 실행을 무조건 막지는 않지만, 실수 지점을 미리 보여 줍니다.

아래를 빌드해 보세요. `string name = null;` 줄은 경고가 나고, `string?`로 바꾼 줄은 의도가 맞습니다.

```csharp
#nullable enable
using System;

class Program
{
    static void Main()
    {
        // string name = null; // 경고: string은 null 불가 의도
        string? name = null;
        if (name == null)
        {
            Console.WriteLine("null");
        }
        else
        {
            Console.WriteLine(name);
        }
    }
}
```

**예상 출력**

```text
null
```

**코드 해석**
- `#nullable enable`이 NRT를 켭니다.
- `string`에 `null`을 넣으면 경고가 납니다. 주석으로 남겼습니다.
- `string? name = null;`은 null 가능 타입이라 의도와 맞습니다.

| 타입 | 의도 | 예 |
|---|---|---|
| `string` | null 불가 | 반드시 문자열 값이 있음 |
| `string?` | null 가능 | 아직 없거나 선택 값 |

### 25-3 null 안전 연산자

**`?.`**는 왼쪽이 null이면 멤버 접근을 하지 않고 null을 돌려줍니다. **`??`**는 왼쪽이 null일 때 오른쪽 기본값을 씁니다. 둘을 이어 `user?.Nickname ?? "Guest"`처럼 쓰면, 사용자가 없거나 닉네임이 없어도 예외 없이 표시 이름을 만들 수 있습니다.

| 표현 | 의미 |
|---|---|
| `?.` | 왼쪽이 null이면 접근을 중단하고 null 반환 |
| `??` | 왼쪽이 null일 때 오른쪽 기본값 사용 |
| `??=` | 왼쪽이 null일 때만 오른쪽 값을 대입 |

먼저 `?.`와 `??`입니다. 아래를 실행해 `user`가 null이어도 `Guest`가 나오는지 확인해 보세요.

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
    }
}
```

**예상 출력**

```text
Guest
```

**코드 해석**
- `user?.Nickname`은 `user`가 null이라 멤버에 들어가지 않고 null입니다.
- `?? "Guest"`가 null 대신 기본 문자열을 채웁니다.
- `NullReferenceException` 없이 `"Guest"`가 출력됩니다.

이어서 **`??=`**입니다. 왼쪽이 null일 때만 오른쪽을 넣습니다. 아래를 실행해 경로가 채워지는지 확인해 보세요.

```csharp
#nullable enable
using System;

class Program
{
    static void Main()
    {
        string? path = null;
        path ??= "/tmp";
        Console.WriteLine(path);
    }
}
```

**예상 출력**

```text
/tmp
```

**코드 해석**
- `path`가 null이므로 `??=`가 `"/tmp"`를 대입합니다.
- 이미 값이 있으면 `??=`는 바꾸지 않습니다.

이 장에서는 null 의도 표시와 안전 연산자까지입니다. 고급 분석기·전체 프로젝트 NRT 마이그레이션은 다루지 않습니다.

### 연습문제

**문제 1**
- 문제: 프로필이 null이어도 예외 없이 표시 이름을 출력하세요.
- 입력: 없음. `User? user = null;`이고 프로퍼티는 `Nickname`
- 출력: `Guest`
- 조건: `?.`와 `??` 사용

**문제 2**
- 문제: 설정 문자열이 null이면 기본 경로를 할당하세요.
- 입력: 없음. `string? path = null;`
- 출력: `/tmp`
- 조건: `??=` 사용. 기본 경로는 `/tmp`

**문제 3**
- 문제: `#nullable enable`에서 `string s = null;`처럼 경고가 나는 코드를 `string?`로 고치고, null일 때 `"empty"`를 출력하세요.
- 입력: 없음
- 출력: `empty`
- 조건: NRT를 켠 상태. `string?`와 null 검사 또는 `??` 사용

### 정답 포인트

- 문제 1: `user?.Nickname ?? "Guest"`
- 문제 2: `path ??= "/tmp";` 후 출력
- 문제 3: `string? s = null;` 뒤 `Console.WriteLine(s ?? "empty");`
- 공통: NRT로 null 가능 여부를 타입에 적고, 실행 시에는 `?.`·`??`·`??=`로 방어한다

---

[상위 문서로 돌아가기](./README.md)
