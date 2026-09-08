# Chapter 16 도전! OOP 미니 프로젝트

## 학습 목표

- 작은 콘솔 전투를 클래스 책임으로 나눠 설계할 수 있다.
- 상속·다형성·추상 클래스를 한 흐름에 통합한다.
- 요구사항을 먼저 정리한 뒤 구현 순서를 잡을 수 있다.

## 본문

### 16-1 요구사항 정리

**OOP**는 Object-Oriented Programming, 즉 **객체지향 프로그래밍**입니다. 데이터와 동작을 객체로 묶어, 역할을 나눈 뒤 서로 협력하게 설계합니다. 도전 장에서는 문법을 새로 배우기보다, 11~15장에서 익힌 클래스·상속·가상 함수·추상 클래스를 **한 프로그램**에 모읍니다.

먼저 “무엇을 만들지”를 짧게 적습니다. 예시는 다음과 같습니다.

1. 플레이어와 적이 번갈아 공격한다.
2. 체력이 0 이하가 되면 전투가 끝난다.
3. 적 종류가 달라도 같은 전투 루프로 처리한다.

기능이 보이면 클래스·함수 후보가 나옵니다. `Player`는 상태, `Enemy`는 적 계약, `Slime`은 구체 적, `Fight`는 전투 규칙처럼 나눌 수 있습니다. 한 클래스에 입력·전투·출력·저장을 모두 넣지 않는 것이 목표입니다.

| 용어 | 의미 |
|---|---|
| 단일 책임 | 한 클래스가 한 가지 역할에 집중한다는 설계 감각 |
| 결합도 | 클래스가 서로의 내부 구현에 얼마나 강하게 묶이는지 |

### 16-2 역할 분리와 골격

역할을 표로 고정하면 구현이 흔들리지 않습니다.

| 클래스 | 책임 |
|---|---|
| `Player` | 이름·체력·공격력. 피해 받기 |
| `Enemy` | 적 공통 계약. `GetName`, `GetAtk`, `Hit`, `IsAlive` |
| `Slime` / `Orc` | 적별 이름·능력치 |
| `Fight` 함수 | 한 턴 공격과 전투 루프 |

`Enemy`는 순수 가상 또는 가상 함수로 “적답게 동작할 계약”을 두고, 구체 적은 파생으로 둡니다. `Fight`는 `Player`와 `Enemy&`만 알면 되므로, 새 적을 추가해도 전투 루프를 거의 건드리지 않아도 됩니다.

아래는 동작하는 최소 골격입니다. 타이핑한 뒤 실행해, 턴 로그와 승패 문구가 나오는지 확인해 보세요.

```cpp
#include <iostream>
#include <string>
using namespace std;

class Player
{
public:
    Player(string name, int hp, int atk)
        : name(name), hp(hp), atk(atk)
    {
    }

    string GetName() const { return name; }
    int GetAtk() const { return atk; }
    bool IsAlive() const { return hp > 0; }

    void Hit(int damage)
    {
        hp -= damage;
        if (hp < 0)
        {
            hp = 0;
        }
    }

    void Print() const
    {
        cout << name << " HP:" << hp << endl;
    }

private:
    string name;
    int hp;
    int atk;
};

class Enemy
{
public:
    virtual ~Enemy() = default;
    virtual string GetName() const = 0;
    virtual int GetAtk() const = 0;
    virtual bool IsAlive() const = 0;
    virtual void Hit(int damage) = 0;
    virtual void Print() const = 0;
};

class Slime : public Enemy
{
public:
    Slime() : hp(30), atk(5)
    {
    }

    string GetName() const override { return "슬라임"; }
    int GetAtk() const override { return atk; }
    bool IsAlive() const override { return hp > 0; }

    void Hit(int damage) override
    {
        hp -= damage;
        if (hp < 0)
        {
            hp = 0;
        }
    }

    void Print() const override
    {
        cout << GetName() << " HP:" << hp << endl;
    }

private:
    int hp;
    int atk;
};

void Fight(Player& player, Enemy& enemy)
{
    while (player.IsAlive() && enemy.IsAlive())
    {
        enemy.Hit(player.GetAtk());
        cout << player.GetName() << " -> " << enemy.GetName()
             << " 피해 " << player.GetAtk() << endl;
        enemy.Print();
        if (!enemy.IsAlive())
        {
            break;
        }

        player.Hit(enemy.GetAtk());
        cout << enemy.GetName() << " -> " << player.GetName()
             << " 피해 " << enemy.GetAtk() << endl;
        player.Print();
    }

    if (player.IsAlive())
    {
        cout << "승리" << endl;
    }
    else
    {
        cout << "패배" << endl;
    }
}

int main()
{
    Player hero("용사", 40, 12);
    Slime slime;
    Fight(hero, slime);
    return 0;
}
```

**예상 출력**

실행할 때마다 같은 규칙이면 비슷한 턴 로그가 이어지고, 마지막에 `승리` 또는 `패배`가 나옵니다. 예시는 용사 공격이 더 커서 보통 `승리`로 끝납니다.

```text
용사 -> 슬라임 피해 12
슬라임 HP:18
슬라임 -> 용사 피해 5
용사 HP:35
용사 -> 슬라임 피해 12
슬라임 HP:6
슬라임 -> 용사 피해 5
용사 HP:30
용사 -> 슬라임 피해 12
슬라임 HP:0
승리
```

**코드 해석**

- `Enemy`는 계약만 두고, `Slime`이 실제 체력·공격을 구현합니다.
- `Fight`는 `Enemy&`만 받으므로 다른 적 타입으로 바꿔도 시그니처는 같습니다.
- `Player`와 `Enemy`는 피해·생존 판정만 담당하고, 루프·승패 문구는 `Fight`가 담당합니다.

### 16-3 다형성으로 적 바꾸기

같은 `Fight`에 `Orc`를 넘기려면 `Enemy`를 상속해 계약만 채우면 됩니다. 전투 함수 안의 `if`로 적 종류를 나누지 않는 것이 핵심입니다. 아래처럼 `Orc`를 추가한 뒤 `main`만 바꿔 보세요.

```cpp
class Orc : public Enemy
{
public:
    Orc() : hp(45), atk(8)
    {
    }

    string GetName() const override { return "오크"; }
    int GetAtk() const override { return atk; }
    bool IsAlive() const override { return hp > 0; }

    void Hit(int damage) override
    {
        hp -= damage;
        if (hp < 0)
        {
            hp = 0;
        }
    }

    void Print() const override
    {
        cout << GetName() << " HP:" << hp << endl;
    }

private:
    int hp;
    int atk;
};

// main 안에서:
// Orc orc;
// Fight(hero, orc);
```

**설계 점검**

| 질문 | 통과 기준 |
|---|---|
| 단일 책임 | 전투 규칙이 `Player` 안에 과도하게 들어 있지 않은가? |
| 확장 | 새 적 추가 시 `Fight`를 거의 안 고치는가? |
| 계약 | `Enemy` 없이 구체 이름만으로 전투를 분기하지 않는가? |

이 장에서는 문법 추가보다 **역할 분리 + 다형성 전투**를 목표로 합니다. 템플릿과 STL 심화는 이후 장에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: 콘솔 전투용 클래스 4개 이상의 이름과 책임을 한 줄씩 적으세요.
- 입력: 없음
- 출력: 표 또는 목록. 예 `Player — 체력과 공격`
- 조건: 상속 관계 1개 이상 포함

**문제 2**

- 문제: `Player`와 `Enemy`가 번갈아 공격하는 전투 루프를 구현하세요.
- 입력: 없음. 초기 체력·공격력은 코드에 상수로 두어도 됩니다.
- 출력: 턴별 피해 로그와 최종 `승리` 또는 `패배`
- 조건: 전투 규칙은 `Player`/`Enemy`와 분리된 함수 또는 클래스에 두기

**문제 3**

- 문제: `Enemy`를 상속한 적 타입을 하나 더 만들고, 같은 전투 함수로 싸워 보세요.
- 입력: 없음
- 출력: 새 적 이름이 로그에 등장
- 조건: 전투 함수 시그니처는 `Enemy` 기준으로 유지. 종류별 `if` 분기 금지

### 정답 포인트

- 문제 1: 예 `Player`, `Enemy`, `Slime` + `Fight` 함수. 상속은 `Slime : Enemy`
- 문제 2: `while (둘 다 생존)` 안에서 상호 `Hit` 후 출력
- 문제 3: 새 파생만 추가하고 `Fight(player, newEnemy)` 호출
- 공통: 구현 전 요구·역할 정리. 클래스는 책임 단위, 연결은 기반 계약

---

[상위 문서로 돌아가기](./README.md)
