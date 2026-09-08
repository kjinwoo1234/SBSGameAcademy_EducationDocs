# Chapter 14 다형성

## 학습 목표

- `virtual` 함수로 실행 시점에 실제 객체 동작을 고를 수 있다.
- 기반 포인터로 파생 객체를 다루는 업캐스팅을 익힌다.
- 기반을 포인터로 삭제할 때 가상 소멸자가 필요한 이유를 설명한다.

## 본문

### 14-1 가상 함수

**다형성**은 같은 함수 호출 문장으로도 **실제 객체 타입**에 따라 다른 동작이 나오는 성질입니다. C++에서는 멤버 함수 앞에 **`virtual`**을 붙이면, 컴파일 시점이 아니라 **실행 시점**에 어느 클래스의 함수를 호출할지 정합니다.

파생 클래스에서 같은 시그니처로 다시 정의할 때는 **`override`**를 붙입니다. `override`는 “기반의 가상 함수를 정확히 재정의한다”는 뜻이고, 이름·인자가 어긋나면 컴파일러가 잡아 줍니다. 가상 함수가 아니면 포인터 타입이 가리키는 **선언 타입**의 함수가 호출되어, 파생의 다른 동작이 나오지 않을 수 있습니다.

| 용어 | 의미 |
|---|---|
| 다형성 | 같은 인터페이스 호출로 객체마다 다른 동작을 수행하는 특성 |
| `virtual` | 실행 시점에 실제 객체 기준으로 호출할 함수를 고르게 하는 지정 |
| `override` | 기반 가상 함수를 재정의함을 컴파일러에 알리는 표시 |

아래를 실행해 `Enemy*`로 받아도 슬라임·오크마다 다른 `Attack`이 나오는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Enemy
{
public:
    virtual ~Enemy() = default;

    virtual void Attack() const
    {
        cout << "Enemy attack" << endl;
    }
};

class Slime : public Enemy
{
public:
    void Attack() const override
    {
        cout << "Slime jump attack" << endl;
    }
};

class Orc : public Enemy
{
public:
    void Attack() const override
    {
        cout << "Orc axe attack" << endl;
    }
};

int main()
{
    Enemy* e1 = new Slime();
    Enemy* e2 = new Orc();
    e1->Attack();
    e2->Attack();
    delete e1;
    delete e2;
    return 0;
}
```

**예상 출력**

```text
Slime jump attack
Orc axe attack
```

**코드 해석**

- `e1`과 `e2`의 타입은 `Enemy*`이지만, `virtual` 덕분에 실제 객체 버전의 `Attack`이 호출됩니다.
- `override`는 기반의 `Attack`을 정확히 덮어썼는지 확인합니다.
- `virtual ~Enemy()`는 아래 소절에서 다루는 안전한 삭제용입니다.

### 14-2 업캐스팅

파생 객체 주소를 기반 클래스 포인터에 담는 것을 **업캐스팅**이라고 합니다. `Enemy* e = new Slime();`처럼 쓰면, 호출 코드는 `Enemy`의 공통 인터페이스만 알면 됩니다. 적 종류가 늘어나도 `Attack`을 호출하는 쪽은 거의 그대로 둘 수 있습니다.

업캐스팅 자체는 보통 암시적으로 됩니다. 반대로 기반 포인터를 파생 포인터로 좁히는 것은 이 장 범위 밖입니다. 지금은 “공통 타입으로 묶어서 같은 방식으로 호출한다”는 흐름만 익히면 됩니다.

배열이나 여러 포인터에 다른 파생 객체를 넣고, 반복문으로 `Attack`만 호출해 보면 다형성의 이점이 잘 보입니다.

아래를 실행해 배열에 담은 적들이 각자 다른 공격을 출력하는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Enemy
{
public:
    virtual ~Enemy() = default;
    virtual void Attack() const
    {
        cout << "Enemy" << endl;
    }
};

class Goblin : public Enemy
{
public:
    void Attack() const override
    {
        cout << "Goblin stab" << endl;
    }
};

class Dragon : public Enemy
{
public:
    void Attack() const override
    {
        cout << "Dragon fire" << endl;
    }
};

int main()
{
    Enemy* enemies[2];
    enemies[0] = new Goblin();
    enemies[1] = new Dragon();

    for (int i = 0; i < 2; i++)
    {
        enemies[i]->Attack();
    }

    delete enemies[0];
    delete enemies[1];
    return 0;
}
```

**예상 출력**

```text
Goblin stab
Dragon fire
```

**코드 해석**

- `enemies`는 `Enemy*` 배열이라 고블린·드래곤을 같은 방식으로 담습니다.
- 루프 안 문장은 `Attack()` 한 줄인데, 객체마다 출력이 다릅니다.
- 사용이 끝나면 각 포인터를 `delete`합니다.

### 14-3 가상 소멸자

기반 포인터로 파생 객체를 `delete`할 수 있다면, 기반 소멸자는 **`virtual`**이어야 안전합니다. 가상 소멸자가 없으면 기반 소멸자만 호출되고, 파생 쪽에서 할 정리가 빠질 수 있습니다. `virtual ~Enemy() = default;`처럼 기본 구현을 두는 형태를 자주 씁니다.

앞 장에서 본 생성·소멸 순서와 맞춰 보면, 가상 소멸자가 있을 때 `delete`는 파생 소멸 → 기반 소멸 순으로 이어집니다. 로그를 넣어 직접 확인하는 연습이 도움됩니다.

아래를 실행해 삭제 시 파생·기반 소멸 로그가 모두 나오는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Base
{
public:
    virtual ~Base()
    {
        cout << "Base 소멸" << endl;
    }
};

class Derived : public Base
{
public:
    ~Derived() override
    {
        cout << "Derived 소멸" << endl;
    }
};

int main()
{
    Base* p = new Derived();
    delete p;
    return 0;
}
```

**예상 출력**

```text
Derived 소멸
Base 소멸
```

**코드 해석**

- `p`의 타입은 `Base*`이지만 실제 객체는 `Derived`입니다.
- 가상 소멸자 덕분에 `delete p`가 파생 소멸부터 호출합니다.
- 순서는 파생 → 기반입니다.

이 장에서는 가상 함수·업캐스팅·가상 소멸자까지 다룹니다. “구현을 강제하는 계약”인 순수 가상 함수와 추상 클래스는 다음 장에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: `Weapon` 기반과 `Sword`, `Bow` 파생을 만들고 `Use()`를 다형성으로 호출하세요.
- 입력: 없음
- 출력:
  ```text
  Sword slash
  Bow shoot
  ```
- 조건: `virtual`과 `override` 사용. 기반 포인터로 호출

**문제 2**

- 문제: 기반·파생 소멸자에 로그를 넣고, 기반 포인터로 `delete`할 때 순서를 확인하세요.
- 입력: 없음
- 출력: 파생 소멸 로그가 먼저, 기반 소멸 로그가 나중에
- 조건: 기반 소멸자는 `virtual`

**문제 3**

- 문제: `Enemy*` 배열 3칸에 서로 다른 파생 적을 넣고 모두 `Attack()` 하세요.
- 입력: 없음
- 출력: 적마다 다른 한 줄 로그 3줄
- 조건: 반복문으로 호출. 끝나면 `delete`

### 정답 포인트

- 문제 1: `Weapon* w = new Sword();` 후 `w->Use();` + `override`
- 문제 2: `virtual ~Base()` 필수. 출력 순서 파생 → 기반
- 문제 3: 업캐스팅으로 배열에 담고 `for`로 `Attack`
- 공통: 다형성 핵심은 기반 인터페이스 + `virtual`. 포인터 삭제 시 가상 소멸자

---

[상위 문서로 돌아가기](./README.md)
