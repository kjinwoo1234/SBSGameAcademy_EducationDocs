# Chapter 07 조건문과 분기

## 학습 목표

- `if` 단독, `if`/`else`, `if`/`else if`, `else if` 여러 개, `if`/`else if`/`else` 전체, `switch`/`case`를 구분해 쓴다.
- **`break`**가 `switch`의 `case`를 끝내는 역할을 실행으로 확인한다.

## 본문

### 07-1 조건문 예약어 한눈에

**조건문**은 상황에 따라 **서로 다른 코드**를 실행하게 만드는 구조입니다. 이 장에서는 **조합을 하나씩** 늘려 갑니다.

| 조합 | 하는 일 | 이럴 때 |
|---|---|---|
| `if`만 | 참일 때만 실행 | 한 갈래 안내 |
| `if` + `else` | 참/거짓 **둘 중 하나** | 합격/불합격 |
| `if` + `else if` | 위에서부터 검사. **맞는 첫 갈래만** | 구간 둘 이상, 나머지 없음 |
| `else if` 여러 개 | 후보를 더 이어 붙임 | A/B/C처럼 갈래가 많을 때 |
| `if` + `else if` + `else` | 위 + 아무 조건도 안 맞으면 `else` | 구간 + 그 외 |
| `switch` + `case` | **하나의 값**이 어느 고정 선택지인지 | 메뉴 번호 |

### 07-2 `if`만 쓰기

**`if (조건)`** 은 조건이 참일 때만 `{ }` 안을 실행합니다. 거짓이면 그 블록을 건너뜁니다.

```c
#include <stdio.h>

int main(void)
{
    int score = 75;

    if (score >= 60)
    {
        printf("합격입니다.\n");
    }

    printf("검사 끝\n");
    return 0;
}
```

**예상 출력**

```text
합격입니다.
검사 끝
```

**코드 해석**

- `75 >= 60`이 참이라 합격 안내가 출력됩니다.
- `score`를 `50`으로 바꾸면 `if` 블록은 건너뛰고 `검사 끝`만 나옵니다.

### 07-3 `if`와 `else`

**`else`**는 `if`가 거짓일 때의 **다른 갈래**입니다. 둘 중 하나만 실행됩니다.

```c
#include <stdio.h>

int main(void)
{
    int score = 50;

    if (score >= 60)
    {
        printf("합격\n");
    }
    else
    {
        printf("불합격\n");
    }

    return 0;
}
```

**예상 출력**

```text
불합격
```

**코드 해석**

- `50 >= 60`이 거짓이라 `else`만 탑니다.

### 07-4 `if`와 `else if`

**`else if`**는 위 조건이 거짓일 때만 다음 후보를 검사합니다. **`else` 없이** 쓰면 아무 조건도 안 맞을 때 **출력이 비어** 있을 수 있습니다.

```c
#include <stdio.h>

int main(void)
{
    int score = 85;

    if (score >= 90)
    {
        printf("A\n");
    }
    else if (score >= 80)
    {
        printf("B\n");
    }

    return 0;
}
```

**예상 출력**

```text
B
```

**코드 해석**

- `85`는 첫 조건이 거짓 → 둘째에서 `B`입니다.
- `score = 70`이면 두 조건 모두 거짓 → 출력이 없습니다.

### 07-5 `else if` 여러 개

**더 좁은 조건을 위**에 둡니다. `>= 60`을 먼저 쓰면 `>= 90`에 영원히 도달하지 못할 수 있습니다.

```c
#include <stdio.h>

int main(void)
{
    int score = 72;

    if (score >= 90)
    {
        printf("A\n");
    }
    else if (score >= 80)
    {
        printf("B\n");
    }
    else if (score >= 70)
    {
        printf("C\n");
    }

    return 0;
}
```

**예상 출력**

```text
C
```

**코드 해석**

- `72`는 90·80을 지나 `>= 70`에서 `C`가 됩니다.

### 07-6 `if`, `else if`, `else` 함께

맨 끝 **`else`**로 “그 외 전부”를 처리합니다.

```c
#include <stdio.h>

int main(void)
{
    int score = 55;

    if (score >= 90)
    {
        printf("A\n");
    }
    else if (score >= 80)
    {
        printf("B\n");
    }
    else if (score >= 70)
    {
        printf("C\n");
    }
    else
    {
        printf("F\n");
    }

    return 0;
}
```

**예상 출력**

```text
F
```

**코드 해석**

- `55`는 위 구간에 안 들어가 `else`의 `F`입니다.

### 07-7 `switch`와 `case`

**`switch`**는 **하나의 값**이 여러 고정 선택지 중 어디인지 나눌 때 읽기 좋습니다.

- **`case 값:`** — 그 값일 때 실행
- **`break;`** — 이 `case`를 끝내고 **`switch` 밖으로** 나감
- **`default:`** — 어느 `case`에도 안 맞을 때

`break`를 빼먹으면 다음 `case`까지 이어 실행되는 **fall-through**가 생길 수 있습니다. 반복문 안의 `break`와 글자는 같지만, 여기서는 **`switch`를 빠져나가는** 뜻입니다. 반복에서의 `break`/`continue`는 **다음 장**에서 확인합니다.

```c
#include <stdio.h>

int main(void)
{
    int menu = 2;

    switch (menu)
    {
    case 1:
        printf("시작\n");
        break;
    case 2:
        printf("설정\n");
        break;
    case 3:
        printf("종료\n");
        break;
    default:
        printf("잘못된 입력\n");
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

- `menu`가 `2`라 `case 2`만 실행되고 `break`로 나갑니다.

`goto`는 레이블로 즉시 점프하는 문법입니다. 학습 단계에서는 “있다” 정도만 알아 두고, 일상 분기는 `if`/`switch`를 씁니다.

| 방식 | 적합한 상황 | 주의점 |
|---|---|---|
| `if` 계열 | 범위·복합 조건 | 조건 순서 |
| `switch` | 값 1개·고정 선택지 | `break`·`default` |

이 장 범위는 **조건 분기**입니다. 반복문은 **다음 장**에서 이어집니다.

### 연습문제

**문제 1**

- 문제: 점수(0~100)를 입력받아 등급을 출력하세요. (`90` 이상 `A`, `80` 이상 `B`, `70` 이상 `C`, 그 외 `F`)
- 입력: 정수 1개
- 출력: `A` / `B` / `C` / `F`
- 조건: `if` / `else if` / `else`. 좁은 조건을 위에

**문제 2**

- 문제: 메뉴 번호 1~3을 `switch`로 출력하세요. 그 외는 `잘못된 입력`
- 입력: 정수 1개
- 출력: `시작` / `설정` / `종료` / `잘못된 입력`
- 조건: 각 `case` 끝 `break`, `default` 포함

**문제 3**

- 문제: 정수 하나를 입력받아 양수·음수·0을 구분해 출력하세요.
- 입력: 정수 1개
- 출력: 세 경우 각각 다른 안내
- 조건: `if` / `else if` / `else`

### 정답 포인트

- 문제 1: `>= 90` → `>= 80` → `>= 70` → `else`
- 문제 2: `case 1/2/3` + `default` + `break`
- 문제 3: `n > 0`, `n < 0`, `else`
- 공통: 분기 **순서**와 `break` 누락을 먼저 점검

---

[상위 문서로 돌아가기](./README.md)
