# Chapter 15 추상 클래스와 인터페이스 설계

## 학습 목표

- 순수 가상 함수로 구현을 강제하는 계약을 만들 수 있다.
- 추상 클래스를 직접 생성하지 않고 파생으로만 쓰는 이유를 설명한다.
- 인터페이스 중심 설계로 호출부와 구체 타입을 분리할 수 있다.

## 본문

### 15-1 순수 가상 함수

앞 장의 `virtual`은 기반에 **기본 구현**을 둘 수도 있었습니다. 그보다 강하게 “파생이 **반드시** 구현해야 한다”고 약속하려면 **순수 가상 함수**를 씁니다. 형태는 `virtual void Render() const = 0;`처럼 선언 끝에 `= 0`을 붙입니다. 본문 `{}`는 두지 않습니다.

순수 가상 함수는 “이름과 인자만 정한 **계약**”입니다. 호출하는 쪽은 `Render`가 있다는 사실만 알면 되고, 화면을 어떻게 그릴지는 각 파생 클래스가 책임집니다. 새 종류를 추가할 때도 계약만 지키면 됩니다.

| 용어 | 의미 |
|---|---|
| 순수 가상 함수 | 파생에서 구현을 강제하는 가상 함수. `= 0`으로 표시 |
| 계약 | 외부에 약속한 함수 이름·역할. 구현 세부는 파생이 담당 |

아래를 실행해 파생마다 다른 `Render`가 호출되는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class IRenderable
{
public:
    virtual ~IRenderable() = default;
    virtual void Render() const = 0;
};

class PlayerSprite : public IRenderable
{
public:
    void Render() const override
    {
        cout << "Render Player" << endl;
    }
};

class EnemySprite : public IRenderable
{
public:
    void Render() const override
    {
        cout << "Render Enemy" << endl;
    }
};

int main()
{
    PlayerSprite p;
    EnemySprite e;
    IRenderable* arr[2];
    arr[0] = &p;
    arr[1] = &e;

    for (int i = 0; i < 2; i++)
    {
        arr[i]->Render();
    }
    return 0;
}
```

**예상 출력**

```text
Render Player
Render Enemy
```

**코드 해석**

- `IRenderable`의 `Render`는 `= 0`이라 순수 가상 함수입니다.
- `PlayerSprite`와 `EnemySprite`가 각각 `override`로 구현합니다.
- 호출 쪽은 `IRenderable*`만 알고 `Render`를 호출합니다.

### 15-2 추상 클래스

순수 가상 함수를 **하나 이상** 가진 클래스는 **추상 클래스**가 됩니다. 추상 클래스는 **직접 객체를 만들 수 없습니다.** `IRenderable r;`처럼 쓰면 컴파일 오류입니다. 객체를 만들려면 모든 순수 가상 함수를 구현한 파생 클래스가 필요합니다.

기반에 공통 데이터나 일반 멤버 함수를 일부 두고, 꼭 달라져야 하는 부분만 순수 가상으로 남기는 설계도 가능합니다. 입문에서는 “계약만 담은 클래스”와 “계약을 채운 구체 클래스”를 나누는 감각을 먼저 익히면 됩니다.

추상 클래스에도 소멸자는 `virtual`로 두는 것이 안전합니다. 앞 장과 같이 기반 포인터로 삭제할 수 있기 때문입니다.

아래처럼 추상 클래스를 직접 만들려 하면 안 된다는 점만 기억하고, 동작하는 쪽은 파생 객체로 확인합니다.

```cpp
#include <iostream>
using namespace std;

class Shape
{
public:
    virtual ~Shape() = default;
    virtual void Draw() const = 0;
};

class Circle : public Shape
{
public:
    void Draw() const override
    {
        cout << "Circle" << endl;
    }
};

int main()
{
    // Shape s; // 오류: 추상 클래스는 객체 생성 불가
    Circle c;
    Shape* p = &c;
    p->Draw();
    return 0;
}
```

**예상 출력**

```text
Circle
```

**코드 해석**

- `Shape`는 순수 가상 `Draw`가 있어 추상 클래스입니다.
- `Circle`이 `Draw`를 구현했으므로 객체를 만들 수 있습니다.
- `Shape* p = &c;`로 업캐스팅한 뒤 `Draw`를 호출합니다.

### 15-3 인터페이스처럼 쓰기

C++에는 별도 `interface` 키워드가 없지만, **순수 가상 함수만 모은 추상 클래스**를 인터페이스처럼 쓰는 경우가 많습니다. 이름에 `I`를 붙이거나 `Movable`처럼 역할만 드러내는 이름을 쓰기도 합니다. 목표는 호출부가 구체 클래스 이름에 덜 묶이게 하는 것입니다.

공통 처리 함수의 인자를 인터페이스 포인터나 참조로 받으면, 나중에 `Player`·`Monster`가 추가되어도 그 함수 시그니처는 그대로 둘 수 있습니다. 새 타입은 계약을 구현하기만 하면 됩니다.

| 구분 | 역할 | 객체 생성 |
|---|---|---|
| 인터페이스형 추상 클래스 | 해야 할 동작만 선언 | 불가 |
| 구체 파생 클래스 | 선언을 실제로 구현 | 가능 |

아래를 실행해 공통 함수가 인터페이스만으로 여러 객체를 처리하는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class IMovable
{
public:
    virtual ~IMovable() = default;
    virtual void Move(int distance) = 0;
};

class Player : public IMovable
{
public:
    void Move(int distance) override
    {
        cout << "Player move " << distance << endl;
    }
};

class Monster : public IMovable
{
public:
    void Move(int distance) override
    {
        cout << "Monster move " << distance << endl;
    }
};

void MoveAll(IMovable* units[], int count, int distance)
{
    for (int i = 0; i < count; i++)
    {
        units[i]->Move(distance);
    }
}

int main()
{
    Player p;
    Monster m;
    IMovable* units[2] = { &p, &m };
    MoveAll(units, 2, 5);
    return 0;
}
```

**예상 출력**

```text
Player move 5
Monster move 5
```

**코드 해석**

- `MoveAll`은 `IMovable*`만 받으므로 `Player`/`Monster` 이름을 몰라도 됩니다.
- 각 클래스가 `Move` 계약을 구현합니다.
- 새 이동 가능 타입을 추가해도 `MoveAll` 본문은 그대로 둘 수 있습니다.

이 장에서는 순수 가상·추상 클래스·인터페이스형 설계까지 다룹니다. 다음 장에서는 지금까지의 OOP를 작은 프로젝트에 묶습니다.

### 연습문제

**문제 1**

- 문제: `IMovable`에 순수 가상 `Move(int)`를 두고 `Player`, `Monster`에 구현하세요.
- 입력: 없음. 이동 거리는 코드에서 `3`처럼 넣어도 됩니다.
- 출력: 각 타입이 한 줄씩 이동 로그
- 조건: 순수 가상 함수 1개 이상. 직접 `IMovable` 객체 생성 금지

**문제 2**

- 문제: 인터페이스 포인터 배열을 받아 모두 `Move`를 호출하는 함수를 작성하세요.
- 입력: 없음
- 출력: 배열에 넣은 객체 수만큼 이동 로그
- 조건: 함수 인자는 기반 포인터 배열. 구체 타입 이름으로 분기하지 않기

**문제 3**

- 문제: `IAttackable`의 `Attack()`을 `Slime`과 `Orc`가 구현하고, 기반 포인터로 각각 호출하세요.
- 입력: 없음
- 출력: 두 종류의 공격 로그
- 조건: 추상 클래스 + `override`. 가상 소멸자 포함

### 정답 포인트

- 문제 1: `virtual void Move(int) = 0;` + 파생에서 `override`
- 문제 2: `void Run(IMovable* arr[], int n)` 안에서 루프로 `Move`
- 문제 3: `IAttackable* a = &slime;` 후 `a->Attack();`
- 공통: 추상 클래스는 직접 생성 불가. 호출부는 계약만 의존

---

[상위 문서로 돌아가기](./README.md)
