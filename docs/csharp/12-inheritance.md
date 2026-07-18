# Chapter 12 상속

## 학습 목표
- 상속 문법과 목적을 이해한다.
- 기반 클래스와 파생 클래스 관계를 설계할 수 있다.
- `base`로 기반 생성자를 호출하고, `protected`로 파생 접근을 구분한다.

## 본문

### 12-1 상속 기본 문법

**상속(inheritance)**은 공통 필드·메서드를 **기반 클래스**에 두고, **파생 클래스**가 `: BaseClass`로 물려받아 확장하는 방법입니다. 중복 코드를 줄이고 “캐릭터 → 전사”처럼 관계를 드러냅니다.

### 12-2 생성자 호출

파생 객체를 `new`하면 **기반 생성자가 먼저** 실행된 뒤 파생 생성자 본문이 이어집니다. 기반에 인자가 필요하면 `public Warrior(int hp) : base(hp)`처럼 **`base(...)`**로 넘깁니다.

### 12-3 접근 제한자와 상속

`private` 멤버는 파생 클래스에서도 **직접 접근 불가**입니다. **`protected`**는 같은 클래스와 **파생 클래스**까지 접근할 수 있어, 상속 계층 안에서만 공유할 상태에 씁니다.

| 제한자 | 파생 클래스에서 직접 접근 | 이럴 때 |
|---|---|---|
| `private` | 불가 | 기반 내부 전용 |
| `protected` | 가능 | 파생과 공유할 상태 |
| `public` | 가능 | 외부에도 공개 |

| 용어 | 의미 |
|---|---|
| 기반 클래스(base class) | 공통 기능을 제공하는 부모 클래스 |
| 파생 클래스(derived class) | 기반 클래스를 확장한 자식 클래스 |

아래 코드를 실행해 `Warrior`가 `Character`의 `hp`를 물려받아 출력하는지 확인해 보세요.

```csharp
using System;

class Character
{
    protected int hp;

    public Character(int hp)
    {
        this.hp = hp;
    }
}

class Warrior : Character
{
    public Warrior(int hp) : base(hp)
    {
    }

    public void PrintHp()
    {
        Console.WriteLine(hp);
    }
}

class Program
{
    static void Main()
    {
        Warrior w = new Warrior(120);
        w.PrintHp();
    }
}
```

**예상 출력**

```text
120
```

**코드 해석**
- `Warrior : Character`로 상속을 선언합니다.
- `base(hp)`가 기반 생성자에 `120`을 넘겨 `protected hp`를 설정합니다.
- `PrintHp`는 파생 클래스라서 `protected hp`를 읽을 수 있습니다.

### 연습문제

**문제 1**
- 문제: `Monster` 기반 클래스와 `BossMonster` 파생 클래스를 작성하세요.
- 입력: 없음(예: `new BossMonster(500)` 후 체력 출력)
- 출력: `500`
- 조건: 파생 생성자에서 `base`로 체력 전달

**문제 2**
- 문제: 기반의 `protected` 필드를 파생 메서드에서 출력하세요.
- 입력: 없음
- 출력: 예: `30` (필드에 넣은 값)
- 조건: 필드는 `protected` (`private`면 파생에서 직접 출력 불가임을 확인할 것)

### 정답 포인트

- 문제 1: `class BossMonster : Monster` + `: base(hp)`
- 문제 2: 기반 `protected int hp;` + 파생 `Console.WriteLine(hp);`
- 공통: 공통 기능은 기반, 접근 제한자로 파생 경계를 설계

---

[상위 문서로 돌아가기](./README.md)
