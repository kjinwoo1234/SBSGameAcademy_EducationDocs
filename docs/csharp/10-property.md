# Chapter 10 프로퍼티

## 학습 목표
- 프로퍼티의 목적과 문법을 이해한다.
- 필드 직접 노출 대신 프로퍼티를 사용하는 이유를 설명할 수 있다.
- 자동 구현 프로퍼티와 `private set`으로 읽기/쓰기 범위를 구분한다.

## 본문

### 10-1 프로퍼티 기본

**프로퍼티**는 필드처럼 `p.Hp`로 쓰되, 안에서는 **get**으로 읽고 **set**으로 쓰는 규칙을 두는 창구입니다. 공개 `public int hp` 필드에 아무 값이나 대입하는 대신, 프로퍼티로 “1 미만은 저장하지 않는다” 같은 검사를 넣을 수 있습니다. 겉모습은 변수에 가깝고, 속은 메서드에 가깝습니다.

`set` 안에 들어오는 대입값은 **`value`**라는 이름으로 씁니다. 실제 값을 담아 두는 칸을 **백킹 필드**라고 부르며, 프로퍼티가 그 칸을 감쌉니다.

| 방식 | 겉으로 보이는 사용 | 장점 | 주의점 |
|---|---|---|---|
| 필드 직접 공개 | `p.hp = -1;` | 단순 | 규칙·검사 넣기 어려움 |
| 프로퍼티 | `p.Hp = 10;` | get/set에 규칙 가능 | 문법을 익혀야 함 |

아래를 실행해 `Level`에 `0`을 넣어도 저장되지 않고, `3`만 남는지 확인해 보세요.

```csharp
using System;

class Player
{
    private int level;

    public int Level
    {
        get { return level; }
        set
        {
            if (value >= 1)
            {
                level = value;
            }
        }
    }
}

class Program
{
    static void Main()
    {
        Player p = new Player();
        p.Level = 0;
        Console.WriteLine(p.Level);
        p.Level = 3;
        Console.WriteLine(p.Level);
    }
}
```

**예상 출력**

```text
0
3
```

**코드 해석**
- `level`은 백킹 필드이고, `Level` 프로퍼티가 읽고 씁니다.
- `set`의 `value`가 대입하려는 값입니다. `0`은 `value >= 1`이 아니어서 저장하지 않습니다.
- `3`은 조건을 통과해 `level`에 들어가고, `get`으로 `3`이 출력됩니다. 처음 `0`은 필드의 기본값입니다.

### 10-2 자동 구현 프로퍼티

값이 단순 보관만 필요하면 **자동 구현 프로퍼티** `public int Score { get; set; }`를 씁니다. 컴파일러가 백킹 필드를 대신 만들므로 필드를 직접 적지 않아도 됩니다. 초기값은 `= 0`처럼 붙일 수 있습니다.

아래를 실행해 `Name`에 쓰고 바로 읽히는지 확인해 보세요.

```csharp
using System;

class Player
{
    public string Name { get; set; } = "";
}

class Program
{
    static void Main()
    {
        Player p = new Player();
        p.Name = "Kim";
        Console.WriteLine(p.Name);
    }
}
```

**예상 출력**

```text
Kim
```

**코드 해석**
- `{ get; set; }`는 읽고 쓸 수 있는 자동 구현 프로퍼티입니다.
- `= ""`는 시작 문자열을 비운 값으로 둡니다.
- `p.Name = "Kim"` 후 출력하면 `Kim`입니다.

### 10-3 접근 제어

`public int Hp { get; private set; }`처럼 **setter만** `private`로 두면, 밖에서는 읽고(`p.Hp`) 쓰기는 클래스 안만 가능합니다. “조회는 공개, 변경은 메서드 경유” 설계에 맞습니다.

객체 만들 때 `new Player { Name = "Kim" }`처럼 중괄호 안에 프로퍼티를 넣는 문법을 **객체 초기화**라고 합니다. `set`이 열려 있는 프로퍼티에만 쓸 수 있습니다.

| 형태 | 외부 읽기 | 외부 쓰기 | 이럴 때 |
|---|---|---|---|
| `{ get; set; }` | 가능 | 가능 | 자유롭게 읽고 쓸 값 |
| `{ get; private set; }` | 가능 | 불가 | 변경은 메서드로만 |
| `{ get; }` | 가능 | 불가 | 생성 시만 설정하는 값 |

| 용어 | 의미 |
|---|---|
| 프로퍼티 | 필드 접근을 제어하는 C# 문법 |
| 백킹 필드 | 프로퍼티가 내부에서 값을 저장하는 실제 필드 |
| `value` | `set`에 대입되는 값 |

아래를 실행해 `Name` 쓰기와 `Hp`의 `private set`을 확인해 보세요. 외부에서 `p.Hp = 50`은 컴파일 오류이고, `Damage`로만 체력이 줄어듭니다.

```csharp
using System;

class Player
{
    public string Name { get; set; } = "";
    public int Hp { get; private set; } = 100;

    public void Damage(int d)
    {
        Hp -= d;
    }
}

class Program
{
    static void Main()
    {
        Player p = new Player { Name = "Kim" };
        p.Damage(20);
        Console.WriteLine($"{p.Name} {p.Hp}");
    }
}
```

**예상 출력**

```text
Kim 80
```

**코드 해석**
- `Name`은 `{ get; set; }`라 객체 초기화와 읽기가 됩니다.
- `Hp`는 `private set`이라 클래스 밖 대입은 안 되고, `Damage`만 체력을 바꿉니다.
- `Damage(20)` 후 `Hp`는 `80`입니다.

이 장에서는 프로퍼티·자동 구현·`private set`까지 다룹니다. `static`은 다음 장에서 이어갑니다.

### 연습문제

**문제 1**
- 문제: 생성 시만 설정 가능한 `Id` 프로퍼티를 설계하세요.
- 입력: 없음. 예로 `new User(10)` 후 `Id` 출력
- 출력: `10`
- 조건: 외부에서 `Id` 수정 불가. `private set` 또는 생성자만 설정

**문제 2**
- 문제: `Level` 프로퍼티를 1 이상만 허용하도록 구현하세요.
- 입력: 없음. 예로 `Level = 0` 시도 후 유효 값 `3` 저장
- 출력: `3`
- 조건: 수동 `get`/`set` 블록에서 `value`로 조건 검사. 0 이하는 저장하지 않음

**문제 3**
- 문제: `Name`은 읽고 쓰고, `Hp`는 `private set`으로 둔 뒤 `Damage(15)` 후 이름과 체력을 한 줄로 출력하세요. 시작 체력은 `100`
- 입력: 없음. 이름은 `"Lee"`
- 출력: `Lee 85`
- 조건: `Main`에서 `Hp` 직접 대입 금지

### 정답 포인트

- 문제 1: 생성자에서만 대입 + `{ get; private set; }` 또는 `{ get; }`
- 문제 2: `set { if (value >= 1) level = value; }` 형태. `value`는 대입값
- 문제 3: `Name` 설정 → `Damage(15)` → `$"{Name} {Hp}"` 출력
- 공통: 필드 공개보다 프로퍼티 공개, 쓰기는 필요할 때만 열기

---

[상위 문서로 돌아가기](./README.md)
