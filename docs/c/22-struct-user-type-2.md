# Chapter 22 구조체와 사용자 정의 자료형 2

## 학습 목표

- `typedef`로 긴 타입 이름에 짧은 별칭을 붙인다.
- 중첩 구조체로 위치·상태처럼 층을 나눈 데이터를 표현한다.
- `union`과 `enum`의 쓰임과, 구조체를 값·포인터로 넘길 때의 차이를 정리한다.

## 본문

### 22-1 `typedef` 별칭

**`typedef`**는 이미 있는 타입에 **다른 이름**을 붙입니다. 타입 자체가 새로 생기는 것이 아니라, 같은 타입을 짧게 부르게 합니다. 구조체에서는 `typedef struct { ... } Point;`처럼 쓰면 변수를 `Point p;`로 선언할 수 있습니다. `struct Point p;`처럼 태그를 반복하지 않아도 됩니다.

함수 포인터나 복잡한 선언이 길어질 때도 별칭이 읽기를 돕습니다. 이 장에서는 구조체·열거형 위주로 씁니다.

| 표기 | 의미 |
|---|---|
| `typedef` | 기존 타입에 별칭 부여 |
| `Point p;` | 별칭으로 선언한 변수 |

아래를 실행해 좌표가 출력되는지 확인해 보세요.

```c
#include <stdio.h>

typedef struct
{
    int x;
    int y;
} Point;

int main(void)
{
    Point p;

    p.x = 10;
    p.y = 20;
    printf("(%d, %d)\n", p.x, p.y);
    return 0;
}
```

**예상 출력**

```text
(10, 20)
```

**코드 해석**

- `Point`는 익명 구조체에 붙인 별칭입니다.
- `Point p;`로 변수를 만들고 `.`으로 멤버에 접근합니다.
- 동작은 `struct` 태그를 쓴 것과 같습니다. 이름만 짧습니다.

### 22-2 중첩 구조체

구조체 멤버로 **다른 구조체**를 두면 중첩 구조체가 됩니다. 예를 들어 `Point` 안에 `x`, `y`를 두고, `Player` 안에 `Point pos`와 체력을 넣으면 “위치와 체력”이 한 타입에 모입니다. 접근은 `player.pos.x`처럼 점을 이어 갑니다.

층을 나누면 좌표만 넘기는 함수와 플레이어 전체를 다루는 함수를 나누기 쉽습니다. 배열·포인터 규칙은 바깥 타입에도 그대로 적용됩니다.

| 표기 | 의미 |
|---|---|
| `player.pos` | 중첩된 `Point` 멤버 |
| `player.pos.x` | 그 안의 `x` |

아래를 실행해 중첩 멤버가 한 줄에 나오는지 확인해 보세요.

```c
#include <stdio.h>

typedef struct
{
    int x;
    int y;
} Point;

typedef struct
{
    Point pos;
    int hp;
} Player;

int main(void)
{
    Player p;

    p.pos.x = 3;
    p.pos.y = 4;
    p.hp = 100;
    printf("%d %d %d\n", p.pos.x, p.pos.y, p.hp);
    return 0;
}
```

**예상 출력**

```text
3 4 100
```

**코드 해석**

- `Player` 안에 `Point pos`가 들어 있습니다.
- `p.pos.x`는 바깥에서 안쪽 멤버로 내려가 접근합니다.
- `hp`는 `Player`의 직접 멤버입니다.

### 22-3 구조체 전달과 반환

값으로 넘기면 **복사본**이 생깁니다. 함수가 멤버를 바꿔도 호출 쪽 원본은 그대로입니다. 포인터로 넘기면 **같은 객체**를 가리키므로 원본이 바뀝니다. 구조체를 **반환**할 때도 값 반환은 복사입니다. 입문에서는 작은 구조체는 값 반환도 쓸 수 있고, 크거나 호출 쪽에서 계속 쓸 객체면 포인터로 다루는 편이 흔합니다.

읽기만 하는 인자는 `const Point *p`처럼 두면 실수로 쓰지 않습니다.

| 방식 | 결과 |
|---|---|
| 값 전달 | 복사. 원본 보호 |
| 포인터 전달 | 공유. 원본 변경 가능 |
| 값 반환 | 복사본을 돌려줌 |

아래를 실행해 값 전달과 포인터 전달 결과를 비교해 보세요.

```c
#include <stdio.h>

typedef struct
{
    int x;
    int y;
} Point;

void move_copy(Point p)
{
    p.x = p.x + 1;
}

void move_ptr(Point *p)
{
    p->x = p->x + 1;
}

int main(void)
{
    Point a = {5, 0};

    move_copy(a);
    printf("after copy: %d\n", a.x);
    move_ptr(&a);
    printf("after ptr: %d\n", a.x);
    return 0;
}
```

**예상 출력**

```text
after copy: 5
after ptr: 6
```

**코드 해석**

- `move_copy`는 복사본만 움직이므로 `a.x`는 `5`입니다.
- `move_ptr`는 `&a`로 원본을 바꾸어 `6`이 됩니다.
- 바꾸려는 목적이면 포인터를 고릅니다.

### 22-4 공용체 `union`

**공용체**는 여러 멤버가 **같은 메모리 영역**을 공유합니다. 한 시점에 의미 있게 쓸 멤버는 보통 하나입니다. `int`로 썼다가 같은 칸을 `float`처럼 읽으면 비트 해석이 달라져 결과가 이상해질 수 있습니다. “지금 어떤 멤버가 유효한지”는 프로그램이 `enum` 등으로 **따로 기억**하는 경우가 많습니다.

크기는 가장 큰 멤버를 담을 만큼이면 됩니다. 메모리를 아끼거나, 같은 비트를 다른 관점으로 볼 때 씁니다. 입문에서는 “공유 칸 + 한 의미”만 기억해도 됩니다.

| 구분 | 구조체 | 공용체 |
|---|---|---|
| 메모리 | 멤버마다 칸 | 멤버가 영역 공유 |
| 동시 사용 | 여러 멤버 동시 OK | 한 시점 한 의미 권장 |

아래를 실행해 나중에 대입한 멤버 값이 출력되는지 확인해 보세요.

```c
#include <stdio.h>

union Number
{
    int i;
    float f;
};

int main(void)
{
    union Number n;

    n.i = 10;
    printf("as int: %d\n", n.i);
    n.f = 2.5f;
    printf("as float: %.1f\n", n.f);
    return 0;
}
```

**예상 출력**

```text
as int: 10
as float: 2.5
```

**코드 해석**

- `n.i`에 쓴 뒤 `n.f`에 쓰면 같은 영역을 `float` 의미로 덮습니다.
- 마지막에 유효한 쪽은 `f`입니다.
- `i`를 다시 읽으면 비트 해석이 달라질 수 있어, 유효 멤버를 코드로 추적합니다.

### 22-5 열거형 `enum`

**열거형**은 상태·종류를 숫자 대신 **이름**으로 씁니다. `enum State { IDLE, RUN, JUMP };`처럼 쓰면 `IDLE`은 보통 `0`, `RUN`은 `1`, `JUMP`는 `2`입니다. `if (st == 1)`보다 `if (st == RUN)`이 의도를 드러냅니다. `switch`와 잘 맞습니다.

이름을 붙인 뒤에는 매직 넘버를 줄이는 것이 목표입니다. 필요하면 `enum State { IDLE = 10, RUN, JUMP };`처럼 시작 값을 지정할 수도 있습니다.

| 요소 | 역할 |
|---|---|
| `enum` | 이름 붙은 정수 상수 묶음 |
| `IDLE`, `RUN` | 열거 상수 |

아래를 실행해 상태 번호와 분기 메시지가 나오는지 확인해 보세요.

```c
#include <stdio.h>

enum State
{
    IDLE,
    RUN,
    JUMP
};

int main(void)
{
    enum State st = RUN;

    printf("state=%d\n", st);
    switch (st)
    {
    case IDLE:
        printf("idle\n");
        break;
    case RUN:
        printf("run\n");
        break;
    case JUMP:
        printf("jump\n");
        break;
    default:
        printf("unknown\n");
        break;
    }
    return 0;
}
```

**예상 출력**

```text
state=1
run
```

**코드 해석**

- `RUN`은 기본으로 `1`입니다.
- `switch`가 `case RUN`으로 들어가 `run`을 출력합니다.
- `default`는 예상 밖 값 대비입니다.

### 연습문제

**문제 1**
- 문제: `typedef struct { char name[20]; int id; int score; } Student;`로 별칭을 두고, 학생 1명의 멤버를 출력하세요.
- 입력: 없음. 예: 이름 `"Kim"`, 학번 `101`, 점수 `88`
- 출력: `Kim 101 88`
- 조건: 변수 타입은 `Student` 사용. `struct Student` 반복 선언 금지

**문제 2**
- 문제: 메뉴 상태를 `enum`으로 정의하고, 정수 입력 `0`/`1`/`2`를 받아 `IDLE`/`RUN`/`JUMP`에 맞춘 메시지를 `switch`로 출력하세요.
- 입력: 정수 1개. 예: `1`
- 출력: `run`
- 조건: `case`에 열거 상수 사용. `default`에서 `unknown` 출력

**문제 3**
- 문제: `Point`를 중첩한 `Player`를 만들고, `void heal(Player *p, int amount)`로 체력을 올린 뒤 출력하세요.
- 입력: 없음. 초기 `pos=(0,0)`, `hp=50`, `heal`로 `+20`
- 출력: `0 0 70`
- 조건: 체력 변경은 포인터로. 위치는 그대로

### 정답 포인트

- 문제 1: `Student s;` 후 `printf("%s %d %d\n", s.name, s.id, s.score);`
- 문제 2: 입력을 `enum State`에 담아 `switch`. `1`이면 `RUN` → `run`
- 문제 3: `p->hp = p->hp + amount;` 호출 `heal(&player, 20);`
- 공통: 별칭·중첩·값/포인터·`enum`/`union` 목적을 구분해 쓰기

---

[상위 문서로 돌아가기](./README.md)
