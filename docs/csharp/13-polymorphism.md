# Chapter 13 다형성과 인터페이스

## 학습 목표
- 오버라이딩 기반 다형성을 이해한다.
- 기반 타입으로 여러 파생 객체를 처리할 수 있다.
- 인터페이스 기반 설계를 적용할 수 있다.

## 본문

### 13-1 가상 메서드

기반 메서드를 **`virtual`**로 두면 파생에서 **`override`**로 내용을 바꿀 수 있습니다. 호출할 때는 변수 타입이 기반이어도 **실제 객체 타입**의 구현이 실행됩니다. 이것이 메서드 오버라이딩 기반 **다형성**입니다.

### 13-2 다형성 호출

`Enemy e = new Orc();`처럼 **기반(또는 인터페이스) 타입 변수**로 파생 객체를 담을 수 있습니다. 배열을 순회하며 `Attack()`만 호출하면, 슬라임·오크마다 다른 출력이 나옵니다. 호출 코드는 같고 동작만 객체마다 다릅니다.

### 13-3 추상 클래스

**`abstract` 클래스**는 그 자체로 `new`할 수 없고, 일부 메서드를 `abstract`로 남겨 **파생이 반드시 구현**하게 합니다. 공통 필드·메서드도 함께 둘 수 있습니다.

### 13-4 인터페이스

**인터페이스(interface)**는 “무엇을 할 수 있는지”(**계약**)만 적고 구현은 클래스에 맡깁니다. 클래스는 인터페이스를 **여러 개** 구현할 수 있습니다. 상속 트리와 별개로 “공격 가능” 같은 능력을 묶을 때 유리합니다.

| 구분 | 추상 클래스 | 인터페이스 |
|---|---|---|
| 인스턴스 생성 | 직접 `new` 불가 | 직접 `new` 불가 |
| 구현 | 일부 구현 + 추상 멤버 가능 | 계약(시그니처) 중심 |
| 다중 | 클래스 상속은 하나 | 여러 인터페이스 구현 가능 |
| 이럴 때 | 공통 뼈대 + 일부 강제 구현 | 능력·역할만 공유 |

| 용어 | 의미 |
|---|---|
| 다형성(polymorphism) | 같은 호출로 객체마다 다른 동작을 수행하는 특성 |
| 추상 클래스 | 직접 생성할 수 없고 파생 클래스 구현을 강제하는 클래스 |
| 인터페이스(interface) | 구현 없이 멤버 시그니처만 정의한 계약 타입 |

아래 코드를 실행해 배열의 각 요소가 다른 `Attack` 메시지를 내는지 확인해 보세요.

```csharp
using System;

interface IAttackable
{
    void Attack();
}

abstract class Enemy : IAttackable
{
    public abstract void Attack();
}

class Slime : Enemy
{
    public override void Attack()
    {
        Console.WriteLine("Slime Attack");
    }
}

class Orc : Enemy
{
    public override void Attack()
    {
        Console.WriteLine("Orc Attack");
    }
}

class Program
{
    static void Main()
    {
        IAttackable[] enemies = { new Slime(), new Orc() };
        foreach (IAttackable e in enemies)
        {
            e.Attack();
        }
    }
}
```

**예상 출력**

```text
Slime Attack
Orc Attack
```

**코드 해석**
- `IAttackable` 배열로 `Slime`·`Orc`를 함께 담습니다.
- `e.Attack()` 호출 시 실제 객체 구현이 실행됩니다.
- `Enemy`는 추상 클래스라 `new Enemy()`는 할 수 없고, 파생만 생성합니다.

### 연습문제

**문제 1**
- 문제: `ISkill` 인터페이스를 만들고 두 스킬 클래스를 구현하세요.
- 입력: 없음
- 출력:
  ```text
  Fire!
  Heal!
  ```
  (메시지는 달라도 됨, 서로 다른 두 줄)
- 조건: 인터페이스 + 구현 클래스 2개 이상, `Use()` 같은 공통 메서드

**문제 2**
- 문제: 인터페이스 타입 **배열**에 구현 객체를 넣고 공통 메서드를 호출하세요.
- 입력: 없음
- 출력: 객체별 메시지 2줄 이상
- 조건: `ISkill[]` (또는 동일 인터페이스 배열) + `foreach` 호출

### 정답 포인트

- 문제 1: `interface ISkill { void Use(); }` + `FireSkill`/`HealSkill`에서 `Use` 구현
- 문제 2: `ISkill[] skills = { new FireSkill(), new HealSkill() };` 후 `foreach`로 `Use()`
- 공통: 계약을 먼저 정하고, 호출부는 인터페이스/기반 타입만 보게 두기

---

[상위 문서로 돌아가기](./README.md)
