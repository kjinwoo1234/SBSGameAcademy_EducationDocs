# Chapter 08 함수와 참조

## 학습 목표

- 함수 **정의·호출·반환** 구조를 이해하고 `void`와 반환형을 구분한다.
- **값 전달**과 **참조 전달**(`int&`)의 차이를 실행으로 확인한다.
- **기본 인자**와 **오버로딩**을 구분해 호출할 수 있다.

## 본문

### 08-1 함수 기본

**함수**는 반복되는 코드를 이름 붙여 묶어 두고, 필요할 때 **호출**해 재사용하는 단위입니다. 형태는 `반환형 이름(매개변수)`입니다. 예로 `int Add(int a, int b)`는 정수 두 개를 받아 정수를 돌려줍니다. 반환형이 **`void`**이면 값을 반환하지 않고, 안에서 `cout`처럼 일만 합니다.

호출은 `Add(3, 4)`처럼 이름 뒤에 괄호를 붙입니다. 괄호 안 값을 **인자**, 선언부 칸을 **매개변수**라고 부릅니다. 함수는 `main` **밖**에 두고, `main`에서 이름으로 부릅니다.

아래를 실행해 `Add`의 반환값과 `Print`의 `void` 차이를 확인해 보세요. `string`을 쓰므로 `#include <string>`을 함께 둡니다.

```cpp
#include <iostream>
#include <string>
using namespace std;

int Add(int a, int b)
{
    return a + b;
}

void Print(const string& message)
{
    cout << message << endl;
}

int main()
{
    int sum = Add(3, 4);
    cout << sum << endl;
    Print("호출 완료");
    return 0;
}
```

**예상 출력**

```text
7
호출 완료
```

**코드 해석**

- `Add`는 `return`으로 합을 돌려주고, `main`이 `sum`에 담아 출력합니다.
- `Print`는 `void`라 반환값이 없고, 안에서 바로 출력합니다.
- `Add`와 `Print`는 `main` 밖에 있습니다.

입문에서는 정의를 `main` 위에 두어도 됩니다. 정의를 아래에 두려면 위에 **선언(프로토타입)**을 먼저 적는 방법도 있습니다. 지금은 정의 위치를 `main` 앞으로 두는 패턴만 써도 충분합니다.

### 08-2 값 전달과 참조 전달

기본은 **값 전달**입니다. 함수에 넘긴 **복사본**이 바뀌어도 호출쪽 변수는 그대로입니다. 호출쪽 변수를 함수가 직접 바꾸려면 **참조 전달** `int& x`를 씁니다. `&`는 기존 변수에 붙는 **또 다른 이름**입니다.

| 방식 | 선언 형태 | 원본 변경 |
|---|---|---|
| 값 전달 | `int x` | 불가 |
| 참조 전달 | `int& x` | 가능 |

먼저 값 전달입니다.

```cpp
#include <iostream>
using namespace std;

void TryDouble(int n)
{
    n = n * 2;
}

int main()
{
    int x = 5;
    TryDouble(x);
    cout << x << endl;
    return 0;
}
```

**예상 출력**

```text
5
```

**코드 해석**

- `TryDouble` 안의 `n`은 복사본이라 `10`이 되어도 `main`의 `x`는 `5`입니다.

이어서 참조 전달로 두 값을 바꿉니다.

```cpp
#include <iostream>
using namespace std;

void SwapByRef(int& a, int& b)
{
    int t = a;
    a = b;
    b = t;
}

int main()
{
    int x = 3, y = 7;
    SwapByRef(x, y);
    cout << x << ' ' << y << endl;
    return 0;
}
```

**예상 출력**

```text
7 3
```

**코드 해석**

- `int& a`는 `x`의 별명이라, 함수 안 교환이 `main`의 `x`, `y`에 그대로 반영됩니다.

### 08-3 기본 인자와 오버로딩

**기본 인자**는 인자를 생략했을 때 자동으로 채워지는 값입니다. **오버로딩**은 같은 함수 이름으로 **매개변수 형태**를 달리해 여러 버전을 만드는 기능입니다. 반환형만 다르게 해서는 구분되지 않습니다.

```cpp
#include <iostream>
using namespace std;

int Scale(int value, int factor = 2)
{
    return value * factor;
}

int Scale(double value)
{
    return static_cast<int>(value * 10);
}

int main()
{
    cout << Scale(5) << endl;
    cout << Scale(5, 3) << endl;
    cout << Scale(1.5) << endl;
    return 0;
}
```

**예상 출력**

```text
10
15
15
```

**코드 해석**

- `Scale(5)`는 기본 `factor = 2`를 씁니다.
- `Scale(5, 3)`은 인자를 명시합니다.
- `Scale(1.5)`는 `double` 버전이 선택됩니다.

이 장 범위는 **함수 · 참조 · 기본 인자 · 오버로딩**입니다. 포인터로 원본을 다루는 법은 **다음 장**에서 이어집니다.

### 연습문제

**문제 1**

- 문제: 정수 2개의 최댓값을 반환하는 함수를 작성하세요.
- 입력: 정수 2개
- 출력: 최댓값 1개
- 조건: 반환형 사용. `main`에서 호출해 출력

**문제 2**

- 문제: 점수를 참조로 받아 10점을 더하는 함수를 작성하세요.
- 입력: 점수 1개
- 출력: 증가 후 점수
- 조건: 원본 값이 실제로 변경되어야 함

**문제 3**

- 문제: 같은 이름 `Print`로 정수 버전과 문자열 버전을 오버로딩하세요.
- 입력: 없음. `main`에서 각각 한 번씩 호출
- 출력: 두 종류의 출력
- 조건: 매개변수 타입으로 구분

### 정답 포인트

- 문제 1: `int Max(int a, int b) { return (a > b) ? a : b; }` 또는 `if`로 비교
- 문제 2: `void AddTen(int& score) { score += 10; }`
- 문제 3: `void Print(int n)` / `void Print(const string& s)`
- 공통: 원본 변경이면 참조(`&`). 오버로딩은 매개변수로 구분

---

[상위 문서로 돌아가기](./README.md)
