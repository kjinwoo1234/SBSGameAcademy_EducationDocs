# Chapter 11 클래스와 객체

## 학습 목표

- 클래스와 객체의 관계를 구분한다.
- 멤버 변수와 멤버 함수로 상태와 동작을 설계할 수 있다.
- 생성자로 초기 상태를 설정하고, `public`/`private`로 접근 범위를 구분한다.

## 본문

### 11-1 클래스 기본

**클래스**는 데이터와 기능을 하나로 묶는 **설계도**입니다. **멤버 변수**는 객체가 가지는 상태이고, **멤버 함수**는 객체가 하는 동작입니다. **객체**는 그 설계도로 만든 **실제 인스턴스**입니다. `Player` 클래스가 있으면 `Player p;`처럼 객체를 만듭니다. C++에서는 앞 장의 `new` 없이도 지역 객체로 바로 둘 수 있습니다.

멤버에 접근할 때는 `p.name`처럼 **점**을 씁니다. 멤버 함수 호출도 `p.PrintStatus();`와 같습니다. 클래스는 `main` 밖이나 위쪽에 정의해 두고, `main`에서 객체를 만들어 사용합니다.

아래를 실행해 멤버에 값을 넣고 멤버 함수로 출력되는지 확인해 보세요.

```cpp
#include <iostream>
#include <string>
using namespace std;

class Player
{
public:
    string name;
    int hp;

    void PrintStatus()
    {
        cout << name << " HP:" << hp << endl;
    }
};

int main()
{
    Player p;
    p.name = "전사";
    p.hp = 100;
    p.PrintStatus();
    return 0;
}
```

**예상 출력**

```text
전사 HP:100
```

**코드 해석**

- `class Player`는 설계도이고, `Player p;`로 객체 `p`를 만듭니다.
- `p.name`, `p.hp`는 멤버 변수로 상태를 담습니다.
- `p.PrintStatus()`는 멤버 함수로 상태를 출력합니다.

### 11-2 생성자

**생성자**는 클래스와 같은 이름의 특수 멤버 함수로, 객체가 만들어질 때 **자동 호출**됩니다. `Player(int hp)`처럼 초기값을 받아 멤버를 설정합니다. 생성자가 있으면 “빈 객체 뒤에 멤버를 하나씩 대입”하는 실수를 줄일 수 있습니다.

멤버를 안정적으로 두려면 **멤버 초기화 목록**을 씁니다. `Player(int hp) : hp(hp) {}`에서 콜론 뒤 `hp(hp)`는 멤버 `hp`를 매개변수 `hp`로 초기화한다는 뜻입니다. 본문 `{}` 안에서 `hp = hp;`처럼 대입하는 방식보다, 생성 시점에 바로 값을 넣는 쪽에 가깝습니다.

매개변수 이름과 멤버 이름이 같을 때는 초기화 목록이나, 본문에서 **`this`**를 씁니다. `this`는 **지금 다루는 객체 자신**을 가리키는 포인터입니다. `this->hp = hp;`는 “이 객체의 멤버 `hp`에 매개변수 `hp`를 넣는다”는 뜻입니다.

아래를 실행해 생성자로 체력을 넣은 뒤 바로 출력되는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Player
{
public:
    int hp;

    Player(int hp) : hp(hp)
    {
    }
};

int main()
{
    Player p(100);
    cout << p.hp << endl;
    return 0;
}
```

**예상 출력**

```text
100
```

**코드 해석**

- `Player p(100);`이 생성자 `Player(int hp)`를 호출합니다.
- `: hp(hp)`로 멤버 `hp`를 `100`으로 초기화합니다.
- 생성 직후 `p.hp`를 출력하면 `100`입니다.

### 11-3 접근 지정자와 캡슐화

**접근 지정자**는 멤버를 어디서 쓸 수 있는지 정합니다. **`public`**은 클래스 밖에서도 호출·접근할 수 있고, **`private`**는 **같은 클래스 안**에서만 씁니다. 멤버 변수는 `private`로 감추고, 필요한 읽기·변경만 `public` 멤버 함수로 열어 두는 방식이 **캡슐화**의 기본입니다.

상태를 바꾸지 않고 읽기만 하는 함수에는 **`const`**를 붙일 수 있습니다. `int GetHp() const`는 이 함수 안에서 멤버를 수정하지 않겠다는 약속입니다. 읽기 전용 조회에 쓰면 의도가 분명해집니다.

| 지정자 | 접근 가능 범위 | 이럴 때 |
|---|---|---|
| `public` | 클래스 밖에서도 사용 | 외부에 공개할 함수·생성자 |
| `private` | 같은 클래스 내부만 | 직접 만지면 위험한 멤버 변수 |

| 용어 | 의미 |
|---|---|
| 인스턴스 | 클래스로부터 생성된 실제 객체 |
| 캡슐화 | 데이터와 동작을 묶고 내부 상태를 보호하는 설계 방식 |
| `this` | 멤버 함수 안에서 현재 객체를 가리키는 포인터 |

아래를 실행해 생성자·`Damage` 후 체력 출력을 확인해 보세요. 밖에서는 `hp`에 직접 손대지 못하고 `GetHp()`로만 읽습니다.

```cpp
#include <iostream>
using namespace std;

class Player
{
private:
    int hp;

public:
    Player(int hp) : hp(hp)
    {
    }

    void Damage(int d)
    {
        hp -= d;
    }

    int GetHp() const
    {
        return hp;
    }
};

int main()
{
    Player p(100);
    p.Damage(15);
    cout << p.GetHp() << endl;
    return 0;
}
```

**예상 출력**

```text
85
```

**코드 해석**

- `private int hp`는 `Player` 안에서만 직접 만질 수 있습니다.
- `Player p(100);`이 생성자로 `hp`를 `100`으로 둡니다.
- `Damage(15)`가 `hp`를 `85`로 바꾸고, 밖에서는 `GetHp()`로만 읽습니다.

이 장에서는 클래스·객체·생성자·`public`/`private`까지 다룹니다. 상속과 가상 함수는 이후 장에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: 이름과 체력을 가지는 `Enemy` 클래스를 작성하세요.
- 입력: 없음. 코드에서 `Enemy e("슬라임", 30);`처럼 만들어도 됩니다.
- 출력: `슬라임 HP:30`처럼 이름·체력을 한 줄로 출력
- 조건: 생성자로 이름·체력 초기화

**문제 2**

- 문제: 체력을 감소시키는 `Hit` 멤버 함수를 구현하세요.
- 입력: 없음. 예로 체력 `30`인 적에 `Hit(10)`
- 출력: `20`
- 조건: 체력 멤버는 `private` 유지, `Hit`는 `public`

**문제 3**

- 문제: `Player`를 체력 `50`으로 만들고 `Damage(20)` 뒤 `GetHp()`로만 남은 체력을 출력하세요.
- 입력: 없음
- 출력: `30`
- 조건: `hp`는 `private`, `main`에서 `p.hp` 직접 대입·읽기 금지

### 정답 포인트

- 문제 1: `private` 또는 `public` 멤버 + `Enemy(string name, int hp)` 생성자 + 출력 멤버 함수
- 문제 2: `void Hit(int dmg) { hp -= dmg; }` 후 체력 출력
- 문제 3: `Player p(50);` → `p.Damage(20);` → `cout << p.GetHp();`
- 공통: 객체는 `Type name(...);`, 멤버는 `private` + 함수로 제어, 초기화 목록 또는 `this->`

---

[상위 문서로 돌아가기](./README.md)
