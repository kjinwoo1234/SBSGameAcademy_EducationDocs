# Chapter 13 상속

## 학습 목표

- 상속으로 공통 기능을 재사용하고 파생 클래스를 확장할 수 있다.
- 기반·파생 생성자 호출 순서를 설명할 수 있다.
- `public` 상속과 `protected` 접근 범위를 구분한다.

## 본문

### 13-1 상속 기본

**상속**은 이미 만든 클래스의 멤버를 물려받아 새 클래스를 만드는 방법입니다. 공통으로 쓰는 상태와 동작은 **기반 클래스**에 두고, 종류마다 다른 부분만 **파생 클래스**에 추가합니다. 문법은 `class Warrior : public Character`처럼 콜론 뒤에 기반 클래스 이름을 적습니다. 여기서 `public` 상속은 입문에서 가장 많이 쓰는 형태입니다.

파생 객체는 기반에 있던 `public` 멤버 함수를 그대로 호출할 수 있습니다. 예를 들어 `Character`에 `GetHp()`가 있으면 `Warrior w(...);` 뒤에도 `w.GetHp();`가 됩니다. 같은 이름·같은 역할의 코드를 여러 클래스에 복붙하지 않아도 됩니다.

| 용어 | 의미 |
|---|---|
| 기반 클래스 | 공통 상태·동작을 제공하는 부모 클래스 |
| 파생 클래스 | 기반을 확장한 자식 클래스 |
| `public` 상속 | 기반의 `public` 멤버가 파생에서도 `public`으로 유지되는 상속 |

아래를 실행해 파생 객체가 기반의 `GetHp`와 자신의 `GetAtk`를 함께 쓰는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Character
{
public:
    Character(int hp) : hp(hp)
    {
    }

    int GetHp() const
    {
        return hp;
    }

protected:
    int hp;
};

class Warrior : public Character
{
public:
    Warrior(int hp, int atk) : Character(hp), atk(atk)
    {
    }

    int GetAtk() const
    {
        return atk;
    }

private:
    int atk;
};

int main()
{
    Warrior w(120, 30);
    cout << "hp: " << w.GetHp() << ", atk: " << w.GetAtk() << endl;
    return 0;
}
```

**예상 출력**

```text
hp: 120, atk: 30
```

**코드 해석**

- `Warrior : public Character`는 `Character`를 상속한다는 뜻입니다.
- `Warrior` 생성자에서 `Character(hp)`로 기반 부분을 먼저 초기화합니다.
- `w.GetHp()`는 기반에 있던 함수이고, `w.GetAtk()`는 파생에 추가한 함수입니다.

### 13-2 생성자·소멸자 호출 순서

파생 객체를 만들면 **기반 생성자가 먼저** 실행되고, 그다음 **파생 생성자**가 실행됩니다. 기반에 쌓인 상태를 먼저 준비한 뒤, 파생만의 멤버를 채우는 순서입니다. 파생 생성자 초기화 목록에서 `Character(hp)`처럼 기반 생성자를 명시하지 않으면, 기반에 기본 생성자가 있을 때만 자동으로 호출됩니다. 매개변수가 있는 기반 생성자만 있으면 반드시 초기화 목록에서 호출해야 합니다.

객체가 사라질 때는 순서가 **반대**입니다. 파생 소멸자가 먼저 돌고, 그다음 기반 소멸자가 돕니다. 지금은 로그로 순서를 눈으로 확인하는 정도면 충분합니다. 가상 소멸자와 포인터 삭제는 다음 장에서 이어갑니다.

아래를 실행해 생성·소멸 로그 순서를 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Base
{
public:
    Base()
    {
        cout << "Base 생성" << endl;
    }

    ~Base()
    {
        cout << "Base 소멸" << endl;
    }
};

class Derived : public Base
{
public:
    Derived()
    {
        cout << "Derived 생성" << endl;
    }

    ~Derived()
    {
        cout << "Derived 소멸" << endl;
    }
};

int main()
{
    Derived d;
    cout << "main 본문" << endl;
    return 0;
}
```

**예상 출력**

```text
Base 생성
Derived 생성
main 본문
Derived 소멸
Base 소멸
```

**코드 해석**

- `Derived d;`를 만들 때 `Base` 생성 → `Derived` 생성 순입니다.
- `main`이 끝나 `d`가 사라질 때는 `Derived` 소멸 → `Base` 소멸 순입니다.

### 13-3 `protected`와 접근 범위

앞 장에서 `private`는 같은 클래스 안에서만 쓴다고 배웠습니다. 상속에서는 **`protected`**가 추가됩니다. `protected` 멤버는 클래스 밖 `main`에서는 직접 만질 수 없지만, **파생 클래스의 멤버 함수 안**에서는 접근할 수 있습니다. 공통 상태를 파생이 쓸 수 있게 열어 두되, 외부에는 감추고 싶을 때 씁니다.

`public` 멤버는 밖에서도 호출할 수 있고, `private` 멤버는 파생에서도 직접 접근하지 못합니다. 파생이 기반의 `private` 값을 쓰려면 기반이 제공하는 `public` 또는 `protected` 함수를 통해야 합니다.

| 지정자 | 같은 클래스 | 파생 클래스 | 클래스 밖 |
|---|---|---|---|
| `public` | 가능 | 가능 | 가능 |
| `protected` | 가능 | 가능 | 불가 |
| `private` | 가능 | 불가 | 불가 |

아래를 실행해 파생 멤버 함수가 `protected` 체력을 읽고 바꾸는지 확인해 보세요. `main`에서 `s.hp`처럼 직접 접근하는 코드는 넣지 않습니다.

```cpp
#include <iostream>
#include <string>
using namespace std;

class Animal
{
public:
    Animal(string name, int hp) : name(name), hp(hp)
    {
    }

    void Print() const
    {
        cout << name << " HP:" << hp << endl;
    }

protected:
    string name;
    int hp;
};

class Dog : public Animal
{
public:
    Dog(string name, int hp) : Animal(name, hp)
    {
    }

    void Heal(int amount)
    {
        hp += amount;
    }
};

int main()
{
    Dog d("멍멍이", 50);
    d.Print();
    d.Heal(10);
    d.Print();
    return 0;
}
```

**예상 출력**

```text
멍멍이 HP:50
멍멍이 HP:60
```

**코드 해석**

- `name`과 `hp`는 `protected`라 `Dog::Heal` 안에서 `hp`를 더할 수 있습니다.
- `main`에서는 `Print`와 `Heal`만 호출하고, 멤버 변수에는 직접 손대지 않습니다.
- `Print`는 기반에 있어 파생 객체에서도 그대로 씁니다.

이 장에서는 상속 문법·생성 순서·`protected`까지 다룹니다. 같은 호출로 객체마다 다른 동작을 만드는 가상 함수는 다음 장에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: `Animal` 기반 클래스와 `Dog` 파생 클래스를 만들고 정보를 출력하세요.
- 입력: 없음. 코드에서 이름·나이를 넣어 생성해도 됩니다.
- 출력: `강아지 초코 나이:3`처럼 종류·이름·나이가 보이게
- 조건: 이름·나이는 기반에 두고, 종류 문자열은 파생에서 출력에 포함

**문제 2**

- 문제: 기반·파생 생성자에 로그를 넣어 호출 순서를 확인하세요.
- 입력: 없음
- 출력:
  ```text
  Base
  Derived
  ```
  처럼 기반이 먼저 나온 뒤 파생이 나오게
- 조건: 파생 객체 하나를 만들고, 생성자에서만 `cout` 사용

**문제 3**

- 문제: `Character`를 상속한 `Mage`에 마나 멤버를 추가하고, 체력·마나를 함께 출력하세요.
- 입력: 없음. 예로 체력 `80`, 마나 `40`
- 출력: `HP:80 MP:40`
- 조건: 체력은 기반의 `protected` 또는 getter로 다루고, `public` 상속 사용

### 정답 포인트

- 문제 1: `class Dog : public Animal` + 기반 생성자 초기화 + 출력 시 파생 정보 추가
- 문제 2: 로그는 생성자 본문에. 순서는 기반 → 파생
- 문제 3: `Mage(int hp, int mp) : Character(hp), mp(mp)` 형태
- 공통: 공통은 기반, 차이는 파생. `private`는 파생에서 직접 접근 불가

---

[상위 문서로 돌아가기](./README.md)
