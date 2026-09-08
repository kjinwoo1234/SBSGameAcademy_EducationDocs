# Chapter 28 콘솔 제어로 뱀/테트리스 만들기

## 학습 목표

- 커서 이동·색·화면 지우기를 작은 유틸 함수로 감싸 쓴다.
- 입력 → 갱신 → 출력 → 대기 게임 루프로 뱀 머리 이동과 벽 충돌을 구현한다.
- 이차원 배열 보드에서 꽉 찬 줄을 찾아 비운다.

## 본문

### 28-1 콘솔 제어 유틸

콘솔 게임은 **좌표에 글자를 찍고**, 필요하면 **색**을 바꾸고, 화면을 **지우는** 도구가 기본입니다. Windows 환경에서는 `windows.h`의 `SetConsoleCursorPosition`, `SetConsoleTextAttribute`를 씁니다. 게임 규칙 코드에 API를 흩뿌리지 말고 `gotoxy`, `set_color`, `clear_screen`처럼 **유틸**로 감싸면 뱀·테트리스가 같은 도구를 씁니다. Chapter 27처럼 `console_util.h` / `console_util.c`로 나누는 것을 권장합니다.

다른 OS에서는 ANSI 이스케이프 등으로 바꿔야 할 수 있습니다. 이 장은 Windows 콘솔을 기준으로 합니다.

| 기능 | Windows API | 유틸 예 |
|---|---|---|
| 커서 이동 | `SetConsoleCursorPosition` | `gotoxy` |
| 글자 색 | `SetConsoleTextAttribute` | `set_color` |
| 화면 지우기 | `system("cls")` 등 | `clear_screen` |
| 대기 | `Sleep` | 틱 속도 |

| 용어 | 의미 |
|---|---|
| 게임 루프 | 입력 → 상태 갱신 → 출력 → 대기를 반복 |
| 틱 | 루프가 한 바퀴 도는 한 번의 갱신 |

아래를 실행해 좌표에 글자가 잠깐 보인 뒤 지워지고 `cleared`가 나오는지 확인해 보세요.

코드에 나오는 Windows 이름은 다음처럼 읽으면 됩니다. `#include <windows.h>`는 이 API 선언을 가져옵니다. **`COORD`**는 커서 좌표용 구조체이고, 멤버 `X`·`Y`에 칸 위치를 넣습니다. **`SHORT`**·**`WORD`**는 Windows가 쓰는 정수 타입 이름입니다. **`GetStdHandle(STD_OUTPUT_HANDLE)`**은 “지금 프로그램의 **표준 출력(콘솔 창)**을 다루는 손잡이(핸들)”를 돌려 줍니다. 커서 이동·색 변경 함수는 이 손잡이에다 “어디로 / 무슨 색으로”를 요청하는 형태입니다. 이 장의 `gotoxy`·`set_color`·`clear_screen`은 **여기서 처음** 쓰는 콘솔 유틸입니다. 앞 장 Chapter 15는 포인터·배열 연습장이라 이 API와는 무관합니다.

`system("cls")`는 운영체제에 화면을 지우라는 명령을 보내는 한 줄입니다. 표준 C만으로는 콘솔 좌표·색을 다루기 어려워, 수업용으로 Windows API와 함께 씁니다.

```c
#include <stdio.h>
#include <stdlib.h>
#include <windows.h>

void gotoxy(int x, int y)
{
    COORD p;

    p.X = (SHORT)x;
    p.Y = (SHORT)y;    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), p);
}

void set_color(WORD color)
{
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
}

void clear_screen(void)
{
    system("cls");
}

int main(void)
{
    clear_screen();
    set_color(FOREGROUND_GREEN | FOREGROUND_INTENSITY);
    gotoxy(5, 2);
    printf("snake");
    Sleep(500);
    clear_screen();
    set_color(FOREGROUND_RED | FOREGROUND_GREEN | FOREGROUND_BLUE);
    printf("cleared\n");
    return 0;
}
```

**예상 출력** (Clear 이후 최종 화면)

```text
cleared
```

**코드 해석**

- `gotoxy`가 출력 위치를 `(5, 2)`로 옮깁니다.
- 초록으로 `snake`를 찍은 뒤 `Sleep`으로 잠깐 멈춥니다.
- 화면을 지우고 색을 되돌린 뒤 `cleared`를 남깁니다.

실습에서는 위 세 함수를 헤더로 분리해도 됩니다. `main`은 데모만 담당합니다.

### 28-2 뱀게임 루프 설계

뱀과 테트리스는 모두 **게임 루프**를 돕니다. 뱀은 매 틱마다 방향을 읽고, 머리 좌표를 한 칸 옮기고, 벽이면 종료하고, 화면을 그린 뒤 `Sleep`으로 속도를 조절합니다. 몸통·먹이·점수까지 넣은 완전체는 이 장에서 한 번에 다 넣지 않습니다. 여기서는 **머리 한 칸 + 벽 충돌**만 따라 합니다.

방향은 `WASD`나 방향키로 받습니다. 입문 예제는 `_kbhit`로 **키가 있을 때만** 읽고, 없으면 이전 방향을 유지합니다. `_getch`로 문자를 읽을 수 있습니다. `conio.h`는 환경에 따라 제공됩니다. Windows + 일반 학습용 컴파일러 조합을 가정합니다.

| 단계 | 할 일 |
|---|---|
| 입력 | `dx`/`dy` 결정 |
| 갱신 | 머리 `x`/`y` 이동, 벽이면 종료 |
| 출력 | 좌표 또는 칸 그리기 |
| 대기 | `Sleep` |

아래를 실행해 키로 머리를 옮기고, `0..9` 밖으로 나가면 `game over`가 나오는지 확인해 보세요. 콘솔 창을 클릭해 포커스를 둔 뒤 조작합니다.

```c
#include <stdio.h>
#include <stdlib.h>
#include <windows.h>
#include <conio.h>

int main(void)
{
    int x = 5;
    int y = 5;
    int dx = 1;
    int dy = 0;
    int size = 10;
    int ch;

    while (1)
    {
        if (_kbhit())
        {
            ch = _getch();
            if (ch == 'a')
            {
                dx = -1;
                dy = 0;
            }
            if (ch == 'd')
            {
                dx = 1;
                dy = 0;
            }
            if (ch == 'w')
            {
                dx = 0;
                dy = -1;
            }
            if (ch == 's')
            {
                dx = 0;
                dy = 1;
            }
        }

        x = x + dx;
        y = y + dy;

        if (x < 0 || x >= size || y < 0 || y >= size)
        {
            printf("game over\n");
            break;
        }

        system("cls");
        printf("x=%d y=%d\n", x, y);
        Sleep(200);
    }
    return 0;
}
```

**예상 출력** (벽 밖으로 나간 뒤 마지막 줄)

```text
game over
```

**코드 해석**

- `dx`/`dy`가 한 틱에 머리가 움직이는 칸 수입니다. 처음에는 오른쪽으로 `1`칸입니다.
- `_kbhit`이 참일 때만 `_getch`로 방향을 바꿉니다.
- `x`/`y`가 `0` 이상 `size` 미만이 아니면 `game over` 후 `break`합니다.
- 매 틱 화면을 지우고 좌표를 출력한 뒤 `Sleep`으로 속도를 늦춥니다.

몸통을 붙일 때는 Chapter 23처럼 `Point` 배열과 `length`로 확장하거나, Chapter 25의 연결 리스트로 확장할 수 있습니다. 핵심 루프 순서는 같습니다.

### 28-3 테트리스 줄 삭제

테트리스도 **입력 → 갱신 → 출력** 루프는 같습니다. 차이는 갱신 규칙입니다. 블록을 내리고 고정한 뒤, 가로가 꽉 찬 줄을 **삭제**합니다. 입문에서는 낙하·회전 전체 대신 **보드에서 꽉 찬 줄 비우기**만 연습합니다.

보드는 **이차원 배열** `int board[행][열]`로 둡니다. 값이 `1`이면 블록이 있는 칸, `0`이면 빈 칸이라고 약속합니다. 아래 줄부터 검사해 한 줄이 모두 `1`이면 위 줄을 한 칸씩 내리고, 맨 위는 `0`으로 채웁니다.

| 단계 | 할 일 |
|---|---|
| 입력 | 좌우·회전 등. 이 소절 예제에서는 생략 |
| 갱신 | 꽉 찬 줄 삭제 |
| 출력 | 보드 상태 확인 |

아래를 실행해 맨 아래 줄이 비고 `cleared=1`이 출력되는지 확인해 보세요.

```c
#include <stdio.h>

int clear_full_lines(int board[][4], int rows)
{
    int r;
    int c;
    int move;
    int cleared = 0;
    int full;

    for (r = rows - 1; r >= 0; r--)
    {
        full = 1;
        for (c = 0; c < 4; c++)
        {
            if (board[r][c] == 0)
            {
                full = 0;
                break;
            }
        }

        if (full)
        {
            cleared++;
            for (move = r; move > 0; move--)
            {
                for (c = 0; c < 4; c++)
                {
                    board[move][c] = board[move - 1][c];
                }
            }
            for (c = 0; c < 4; c++)
            {
                board[0][c] = 0;
            }
            r++;
        }
    }
    return cleared;
}

int main(void)
{
    int board[3][4] = {
        {0, 0, 0, 0},
        {1, 0, 0, 0},
        {1, 1, 1, 1}
    };
    int n;

    n = clear_full_lines(board, 3);
    printf("cleared=%d\n", n);
    printf("bottom=%d%d%d%d\n",
           board[2][0], board[2][1], board[2][2], board[2][3]);
    return 0;
}
```

**예상 출력**

```text
cleared=1
bottom=1000
```

**코드 해석**

- `board`는 3행 4열입니다. 맨 아래 행을 모두 `1`로 채워 꽉 찬 줄로 둡니다.
- `clear_full_lines`는 아래 줄부터 검사해 꽉 찬 줄을 지우고 위를 한 칸씩 내립니다.
- 삭제 후 맨 아래는 `1000`처럼 한 칸만 블록이 남은 상태가 됩니다.
- `r++`는 한 줄을 지운 뒤 같은 행 번호를 다시 검사하기 위함입니다.

낙하 블록은 `Point`나 작은 이차원 조각 배열로 두고, 매 틱 `y`를 늘리다가 바닥·고정 블록과 겹치면 보드에 `1`을 찍는 식으로 확장합니다. 이 장에서는 콘솔 유틸, 뱀 머리 루프, 줄 삭제 핵심까지입니다. 완성 상용 게임은 다음 단계로 미룹니다.

### 연습문제

**문제 1**
- 문제: `gotoxy`, `set_color`, `clear_screen`을 `console_util.h` / `console_util.c`로 나누고, 데모 `main`에서 특정 좌표에 색 글자를 쓴 뒤 지우고 `ok`를 출력하세요.
- 입력: 없음
- 출력:
  ```text
  ok
  ```
- 조건: Chapter 27처럼 헤더에 선언, 소스에 정의. include guard 사용

**문제 2**
- 문제: 뱀 머리 1칸 이동과 벽 충돌 종료를 구현하세요.
- 입력: `WASD` 키. 콘솔 포커스 필요
- 출력: 매 틱 `x=… y=…` 형태, 벽 밖이면 마지막에
  ```text
  game over
  ```
- 조건: `while` 루프 + `Sleep` + 키 있을 때만 입력. 벽 범위는 본문처럼 `0..9` 또는 스스로 정한 크기

**문제 3**
- 문제: 가로 4칸 보드에서 꽉 찬 줄을 삭제하는 함수를 작성하세요.
- 입력: 없음. 본문처럼 맨 아래 줄이 모두 `1`인 `int board[3][4]`를 만들어 호출
- 출력:
  ```text
  cleared=1
  ```
  추가로 삭제 후 그 줄이 비었거나 위 블록이 내려온 상태를 숫자로 확인해도 됨
- 조건: 아래 줄부터 검사. 이차원 배열 사용

### 정답 포인트

- 문제 1: 유틸 분리 + `main`에서 `ok` 출력
- 문제 2: 위치 갱신 후 범위 밖이면 `game over` 후 `break`
- 문제 3: 줄이 모두 `1`이면 위 행을 복사해 내리고 맨 위를 `0`으로. `cleared` 증가
- 공통: 콘솔 API는 유틸로, 뱀/테트리스는 루프는 같고 갱신 규칙만 다름

---

[상위 문서로 돌아가기](./README.md)
