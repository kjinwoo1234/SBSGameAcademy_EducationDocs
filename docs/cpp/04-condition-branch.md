# Chapter 04 조건문

## 학습 목표

- `if` 단독, `if`/`else`, `if`/`else if`, `else if` 여러 개, `if`/`else if`/`else` 전체, `switch`/`case`를 구분해 쓴다.
- **`break`**가 `switch`의 `case`를 끝내는 역할을 실행으로 확인한다.

## 본문

### 04-1 조건문 예약어 한눈에

**조건문**은 상황에 따라 **서로 다른 코드**를 실행하게 만드는 구조입니다. 예약어가 여러 개라서, 한 줄에 `if`·`else if`·`else`·`switch`를 한꺼번에 외우면 섞이기 쉽습니다. 이 장에서는 **조합을 하나씩** 늘려 갑니다.

| 조합 | 하는 일 | 이럴 때 |
|---|---|---|
| `if`만 | 참일 때만 실행. 거짓이면 그 블록 통째로 건너뜀 | “합격이면 안내만”처럼 **한 갈래** |
| `if` + `else` | 참/거짓 **둘 중 하나**만 실행 | 합격/불합격처럼 **이분** |
| `if` + `else if` | 위에서부터 조건 검사. **맞는 첫 갈래만** 실행 | 구간이 둘 이상, **나머지 처리 없음** |
| `else if` 여러 개 | 후보를 더 이어 붙임 | A/B/C처럼 **갈래가 많을 때** |
| `if` + `else if` + `else` | 위와 같고, 아무 조건도 안 맞으면 `else` | 구간 + **그 외 전부** |
| `switch` + `case` | **하나의 값**이 어느 고정 선택지인지 | 메뉴 번호, 상태 코드 |

### 04-2 `if`만 쓰기

**`if (조건)`** 은 조건이 **참**일 때만 `{ }` 안을 실행합니다. 거짓이면 그 블록은 실행하지 않고, 바로 다음 줄로 갑니다.

아래를 실행해, 점수를 `50`으로 바꿔 보면 안내가 **안 나오는지**도 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

int main()
{
    int score = 75;

    if (score >= 60)
    {
        cout << "합격입니다." << endl;
    }

    cout << "검사 끝" << endl;
    return 0;
}
```

**예상 출력** (`score = 75`)

```text
합격입니다.
검사 끝
```

**코드 해석**

- `75 >= 60`이 참이라 `합격입니다.`가 출력됩니다.
- `score`를 `50`으로 바꾸면 `if` 블록은 건너뛰고 `검사 끝`만 나옵니다.

### 04-3 `if`와 `else`

**`else`**는 `if`가 **거짓**일 때 실행하는 **다른 갈래**입니다. 참이면 `if`만, 거짓이면 `else`만 실행됩니다.

```cpp
#include <iostream>
using namespace std;

int main()
{
    int score = 50;

    if (score >= 60)
    {
        cout << "합격" << endl;
    }
    else
    {
        cout << "불합격" << endl;
    }

    return 0;
}
```

**예상 출력**

```text
불합격
```

**코드 해석**

- `50 >= 60`이 거짓이라 `else`의 `불합격`만 출력됩니다.
- `if`와 `else`는 **둘 중 하나만** 탑니다.

### 04-4 `if`와 `else if`

**`else if`**는 “위 조건이 거짓이었을 때만” **다음 후보**를 검사합니다. 위에서 이미 참인 갈래를 타면, 아래 `else if`는 보지 않습니다. **`else` 없이** 쓰면, 어느 조건도 안 맞을 때는 **아무 블록도 실행되지 않습니다.**

```cpp
#include <iostream>
using namespace std;

int main()
{
    int score = 85;

    if (score >= 90)
    {
        cout << "A" << endl;
    }
    else if (score >= 80)
    {
        cout << "B" << endl;
    }

    return 0;
}
```

**예상 출력**

```text
B
```

**코드 해석**

- `85`는 90 미만이라 첫 `if`는 거짓 → `else if`로 넘어갑니다.
- `85 >= 80`이 참이라 `B`만 출력됩니다.
- `score = 70`이면 두 조건 모두 거짓 → **출력이 비어** 있습니다.

### 04-5 `else if` 여러 개 쓰기

`else if`는 **필요한 만큼** 이어 붙일 수 있습니다. 검사는 항상 **위 → 아래**입니다. **더 좁은 조건을 위**에 두는 편이 안전합니다. `if (score >= 60)`를 먼저 쓰고 아래에 `else if (score >= 90)`을 두면, 90점도 이미 첫 갈래에 잡혀 A에 **도달하지 못합니다.**

```cpp
#include <iostream>
using namespace std;

int main()
{
    int score = 72;

    if (score >= 90)
    {
        cout << "A" << endl;
    }
    else if (score >= 80)
    {
        cout << "B" << endl;
    }
    else if (score >= 70)
    {
        cout << "C" << endl;
    }

    return 0;
}
```

**예상 출력**

```text
C
```

**코드 해석**

- `72`는 90·80 조건을 통과하지 못하고, `>= 70`에서 `C`가 됩니다.
- 조건 순서를 바꾸면 같은 점수라도 **다른 글자**가 나올 수 있습니다.

### 04-6 `if`, `else if`, `else` 함께

맨 끝에 **`else`**를 붙이면, 위 조건이 **모두 거짓**일 때의 **나머지**를 처리합니다.

```cpp
#include <iostream>
using namespace std;

int main()
{
    int score = 55;

    if (score >= 90)
    {
        cout << "A" << endl;
    }
    else if (score >= 80)
    {
        cout << "B" << endl;
    }
    else if (score >= 70)
    {
        cout << "C" << endl;
    }
    else
    {
        cout << "F" << endl;
    }

    return 0;
}
```

**예상 출력**

```text
F
```

**코드 해석**

- `55`는 위 구간 조건에 안 들어가 `else`의 `F`가 출력됩니다.

| 방식 | 적합한 상황 | 주의점 |
|---|---|---|
| `if`만 | 한 갈래만 필요할 때 | 거짓이면 아무 일도 안 함 |
| `if` / `else` | 참·거짓 이분 | 조건식 실수 시 반대 갈래 |
| `if` / `else if` | 여러 구간, 나머지 불필요 | 순서·빈 경우 |
| `if` / `else if` / `else` | 여러 구간 + 나머지 | 좁은 조건을 위에 |

### 04-7 `switch`와 `case`

**`switch`**는 **하나의 값**이 여러 **고정 선택지** 중 어디에 해당하는지 나눌 때 읽기 좋습니다.

- **`case 값:`** — 그 값일 때 실행할 코드
- **`break;`** — 이 `case` 처리를 끝내고 **`switch` 밖으로** 나감
- **`default:`** — 어느 `case`에도 안 맞을 때

입문에서는 **한 `case` → 처리 → `break`** 패턴부터 익히세요. `break`를 빼먹으면 다음 `case`까지 이어 실행되는 **fall-through**가 생길 수 있습니다.

```cpp
#include <iostream>
using namespace std;

int main()
{
    int menu = 2;

    switch (menu)
    {
    case 1:
        cout << "시작" << endl;
        break;
    case 2:
        cout << "설정" << endl;
        break;
    case 3:
        cout << "종료" << endl;
        break;
    default:
        cout << "잘못된 입력" << endl;
        break;
    }

    return 0;
}
```

**예상 출력**

```text
설정
```

**코드 해석**

- `menu`가 `2`라 `case 2`만 실행되고 `break`로 `switch`를 빠져나갑니다.
- `menu = 9`면 `default`의 `잘못된 입력`이 나옵니다.

| 방식 | 적합한 상황 | 주의점 |
|---|---|---|
| `if` 계열 | 범위·복합 조건 | 조건 순서 |
| `switch` | 값 1개·고정 선택지 | `default`·`break` 누락 |

이 장 범위는 **조건 분기**입니다. 반복문은 **다음 장**에서 이어집니다.

### 연습문제

앞 장처럼 `cin`으로 정수를 받은 뒤, 이 장에서 배운 분기로 처리하면 됩니다.

**문제 1**

- 문제: 점수(0~100)를 입력받아 등급을 출력하세요. (`90` 이상 `A`, `80` 이상 `B`, `70` 이상 `C`, 그 외 `F`)
- 입력: 정수 1개
- 출력: `A` / `B` / `C` / `F`
- 조건: `if` / `else if` / `else` 사용. 좁은 조건을 위에 둘 것

**문제 2**

- 문제: 메뉴 번호를 입력받아 다른 문장을 출력하세요. (`1` 시작, `2` 설정, `3` 종료, 그 외 잘못된 입력)
- 입력: 메뉴 번호 1개
- 출력: 기능명 또는 `잘못된 입력`
- 조건: `switch`와 `default` 사용

### 정답 포인트

- 문제 1: `>= 90` → `>= 80` → `>= 70` → `else`
- 문제 2: `case 1/2/3` + `default`, 각 `case` 끝 `break`
- 공통: 문법보다 **분기 순서**를 먼저 설계

---

[상위 문서로 돌아가기](./README.md)
