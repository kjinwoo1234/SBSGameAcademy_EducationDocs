# Chapter 15 도전! 프로그래밍 2

## 학습 목표
- 콘솔 제어와 함수 분할을 실전 문제에 적용한다.
- 작은 프로젝트 단위로 기능을 조립해본다.

## 본문

### 15-1 커서 이동 함수

콘솔에서 **커서**는 다음에 `printf`가 글자를 찍을 위치입니다. 평소에는 한 줄씩 아래로만 내려가지만, **원하는 (x, y) 좌표**로 커서를 옮기면 같은 자리에 상태판·맵을 다시 그리기 쉽습니다. 이런 일을 하는 함수를 예로 `gotoxy`처럼 이름 붙여 두면, 게임 UI에서 반복해서 쓰기 좋습니다.

이 과정은 **Windows + Visual Studio(`cl`)** 기준입니다. Windows가 제공하는 콘솔 함수를 쓰려면 `#include <windows.h>`가 필요합니다. 커서를 옮길 때는 `SetConsoleCursorPosition`을 부르고, 출력 창 손잡이는 `GetStdHandle(STD_OUTPUT_HANDLE)`로 얻습니다. 좌표는 `COORD` 구조체에 `X`, `Y`를 넣어 넘깁니다. (`SHORT`는 Windows가 쓰는 짧은 정수 타입 이름입니다.)

```c
#include <stdio.h>
#include <windows.h>

void gotoxy(int x, int y) {
    COORD p;
    p.X = (SHORT)x;
    p.Y = (SHORT)y;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), p);
}

int main(void) {
    gotoxy(5, 2);
    printf("여기(5, 2)에 출력");
    return 0;
}
```

`gotoxy(5, 2)` 다음에 `printf`를 하면, 글자가 화면의 대략 그 위치부터 찍힙니다. (창 크기·폰트에 따라 보이는 칸은 조금 다를 수 있습니다.)

### 15-2 글자 색상 변경 함수

글자 색을 바꾸는 함수(예: `setColor` / `set_color`)를 따로 두면, 경고·정상·이벤트 메시지를 **색으로 구분**하기 좋습니다. Windows에서는 `SetConsoleTextAttribute`에 색 번호(`WORD` 타입)를 넘깁니다. 예: `FOREGROUND_RED | FOREGROUND_INTENSITY`는 밝은 빨간색 계열입니다.

```c
#include <windows.h>

void set_color(WORD color) {
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
}
```

출력 전에 `set_color(...)`를 호출하고, 끝나면 기본 색으로 다시 돌려 두는 습관이 좋습니다.

### 15-3 글자 전체를 지우는 함수

화면을 통째로 비우는 함수(예: `clearScreen` / `clear_screen`)를 쓰면, 매 프레임마다 **깨끗한 상태에서 다시 그리는** 흐름을 만들기 쉽습니다. 입문에서는 `system("cls");`로 콘솔을 지우는 방법이 흔합니다. (`#include <stdlib.h>`가 필요할 수 있습니다.)

```c
#include <stdlib.h>

void clear_screen(void) {
    system("cls");
}
```

세 함수를 이어서 쓰는 최소 예는 다음과 같습니다. `clear_screen` → `gotoxy` → `set_color` → `printf` 순으로 “지우고, 자리 잡고, 색 정하고, 출력”합니다.

```c
#include <stdio.h>
#include <stdlib.h>
#include <windows.h>

void gotoxy(int x, int y) {
    COORD p;
    p.X = (SHORT)x;
    p.Y = (SHORT)y;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), p);
}

void set_color(WORD color) {
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
}

void clear_screen(void) {
    system("cls");
}

int main(void) {
    clear_screen();
    gotoxy(0, 0);
    set_color(FOREGROUND_GREEN | FOREGROUND_INTENSITY);
    printf("HP: 100");
    set_color(FOREGROUND_RED | FOREGROUND_GREEN | FOREGROUND_BLUE);
    return 0;
}
```

더 큰 뱀/테트리스용 콘솔 모듈은 **Chapter 28**에서 이어서 다룹니다.

### 15-4 머드게임 만들기

입력 처리, 상태 갱신, 화면 출력을 **서로 다른 함수**로 나누면 버그를 찾거나 기능을 바꿀 때 부담이 줄어듭니다. 예를 들어 `init_game()`은 시작 값만, `handle_input()`은 키·명령만, `update_game()`은 HP·위치 같은 규칙만, `render_game()`은 화면에 찍는 일만 맡기는 식으로 쪼갤 수 있습니다.

게임 루프는 보통 **입력 → 상태 업데이트 → 렌더링** 순으로 돌아갑니다.

| 단계 | 역할 |
|---|---|
| 입력 | 키/명령 수집 |
| 업데이트 | 캐릭터/상태 계산 |
| 렌더링 | 화면 반영 |

아래는 그 뼈대만 보여 주는 템플릿입니다. 실제 게임에서는 `main` 루프에 **종료 조건**(예: ESC)과 **잘못된 입력**에 대한 재시도를 넣어 완성도를 올리면 됩니다.

```c
#include <stdio.h>

void init_game(void) { printf("init\n"); }
void handle_input(void) { /* 입력 처리 */ }
void update_game(void) { /* 상태 갱신 */ }
void render_game(void) { /* 화면 출력 */ }

int main(void) {
    init_game();
    for (int frame = 0; frame < 3; frame++) {
        handle_input();
        update_game();
        render_game();
    }
    return 0;
}
```

**예상 출력**

```text
init
```

### 연습문제

**문제 1**
- 문제: 커서 이동 함수와 색상 출력 함수를 만들어 플레이어 이름·HP·EXP가 보이는 상태판(UI)을 한 번 출력하세요.
- 입력: 플레이어 이름, HP, EXP 초기값
- 출력: 콘솔 좌표 기반 상태판 1회 출력
- 조건(힌트): 출력 관련 코드는 최소 두 개 이상의 함수로 나누세요.

**문제 2**
- 문제: 플레이어 HP/EXP를 갱신하는 로직을 함수로 분리하고, `handle_input`, `update_game`, `render_game` 구조를 유지한 채 메인 루프에서 호출하세요.
- 입력: 반복 루프마다 들어오는 사용자 입력
- 출력: 입력에 따라 갱신된 HP/EXP 상태
- 조건(힌트): 메인 루프는 입력 -> 업데이트 -> 렌더링 순서를 유지하세요.

### 정답 포인트

전역변수는 줄이고, 기능마다 함수를 나누며, 입력·업데이트·출력 루프를 일정하게 유지하면 됩니다.

---

[상위 문서로 돌아가기](./README.md)
