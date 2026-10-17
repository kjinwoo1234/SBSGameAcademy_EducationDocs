# Chapter 27 콘솔 제어로 뱀/테트리스 만들기

## 학습 목표

- 커서 이동·색·화면 지우기로 콘솔 화면을 제어할 수 있다.
- 입력 → 갱신 → 출력 → 대기 게임 루프로 뱀 머리 이동·벽 충돌을 구현할 수 있다.
- 이차원 배열 보드에서 꽉 찬 줄을 찾아 비울 수 있다.
- 몸통·먹이·점수를 더해 뱀 게임 하나를 끝까지 완성할 수 있다.

## 본문

### 27-1 콘솔 제어 함수

Windows 콘솔은 **좌표·색·지우기**만으로도 간단한 게임 화면을 만들 수 있습니다. **`SetConsoleCursorPosition`**은 다음에 쓸 글자의 칸을 옮깁니다. 왼쪽 위가 `(0, 0)`입니다. **`SetConsoleTextAttribute`**에 색 값을 넣으면 글자 색이 바뀝니다. **`system("cls")`**는 화면을 비웁니다. **`Sleep(밀리초)`**는 그 시간만큼 잠시 멈춥니다. `<windows.h>`가 필요합니다.

게임 코드에 API를 흩뿌리지 말고 유틸 클래스로 감싸면 뱀·테트리스가 같은 도구를 씁니다.

`<windows.h>`에서 처음 보는 이름은 아래처럼 읽습니다.

| 이름 | 읽는 법 |
|---|---|
| `COORD` | 커서 좌표용 구조체. 멤버 `X`·`Y`에 칸 위치 |
| `SHORT`, `WORD` | Windows가 쓰는 정수 타입 이름 |
| `GetStdHandle(STD_OUTPUT_HANDLE)` | 표준 출력인 콘솔 창을 다루는 손잡이. **핸들**이라고도 부름 |

커서 이동·색 변경 함수는 이 핸들에 "어디로 / 무슨 색으로"를 요청하는 형태입니다. 표준 C++만으로는 콘솔 좌표·색을 다루기 어려워, 수업용으로 Windows API와 함께 씁니다. 이 장은 Windows 콘솔을 기준으로 합니다.

| 기능 | 사용형 | 용도 |
|---|---|---|
| 커서 이동 | `SetConsoleCursorPosition(handle, coord)` | 좌표에 출력 |
| 글자 색 | `SetConsoleTextAttribute(handle, color)` | 상태 강조 |
| 화면 지우기 | `system("cls")` | 화면 초기화 |
| 대기 | `Sleep(200)` | 틱 속도 조절 |

| 용어 | 의미 |
|---|---|
| 게임 루프 | 입력 → 상태 갱신 → 출력 → 대기를 반복하는 구조 |
| 틱 | 루프가 한 바퀴 도는 한 번의 갱신 단위 |

아래를 실행해 좌표 `(5, 2)`에 초록 글자가 잠깐 보인 뒤 화면이 지워지고 `cleared`가 출력되는지 확인해 보세요.

```cpp
#include <iostream>
#include <windows.h>
using namespace std;

class ConsoleUtil
{
public:
    static void MoveTo(int x, int y)
    {
        COORD pos = {static_cast<SHORT>(x), static_cast<SHORT>(y)};
        SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), pos);
    }

    static void SetColor(WORD color)
    {
        SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
    }

    static void Clear()
    {
        system("cls");
    }
};

int main()
{
    ConsoleUtil::Clear();
    ConsoleUtil::SetColor(FOREGROUND_GREEN | FOREGROUND_INTENSITY);
    ConsoleUtil::MoveTo(5, 2);
    cout << "snake";
    Sleep(500);
    ConsoleUtil::Clear();
    ConsoleUtil::SetColor(FOREGROUND_RED | FOREGROUND_GREEN | FOREGROUND_BLUE);
    cout << "cleared" << endl;
    return 0;
}
```

**예상 출력** (Clear 이후 최종 화면)

```text
cleared
```

**코드 해석**

- 유틸이 커서·색·Clear를 감쌉니다.
- `(5, 2)`에 `snake`를 그린 뒤 지우고 `cleared`를 남깁니다.
- 마지막에 기본에 가까운 색으로 되돌립니다.

### 27-2 뱀게임 루프 설계

뱀과 테트리스는 모두 **게임 루프**를 돕니다. 뱀은 매 틱마다 방향을 읽고, 머리 좌표를 한 칸 옮기고, 벽이면 종료하고, 화면을 그린 뒤 `Sleep`으로 속도를 조절합니다. 여기서는 먼저 **머리 한 칸 + 벽 충돌**만 따라 합니다. 몸통·먹이·점수는 27-5에서 더해 완성합니다.

방향키는 **`GetAsyncKeyState`**로 읽습니다. 루프 안에서 매번 키를 기다리면 멈춘 느낌이 나므로, 입문 예제는 **키가 눌린 동안** 방향을 바꾸고 없으면 이전 방향을 유지합니다. `VK_LEFT` 등은 Windows 가상 키 코드입니다.

| 단계 | 할 일 |
|---|---|
| 입력 | 방향키로 `dx`/`dy` 결정 |
| 갱신 | 머리 `x`/`y` 이동, 벽이면 종료 |
| 출력 | 좌표 출력 |
| 대기 | `Sleep` |

아래를 실행해 방향키로 머리를 옮기고, 벽 `0..9` 밖으로 나가면 `game over`가 나오는지 확인해 보세요. 콘솔 창을 클릭해 포커스를 둔 뒤 조작합니다.

```cpp
#include <iostream>
#include <windows.h>
using namespace std;

int main()
{
    int x = 5;
    int y = 5;
    int dx = 1;
    int dy = 0;
    int size = 10;

    while (true)
    {
        if (GetAsyncKeyState(VK_LEFT) & 0x8000)
        {
            dx = -1;
            dy = 0;
        }
        if (GetAsyncKeyState(VK_RIGHT) & 0x8000)
        {
            dx = 1;
            dy = 0;
        }
        if (GetAsyncKeyState(VK_UP) & 0x8000)
        {
            dx = 0;
            dy = -1;
        }
        if (GetAsyncKeyState(VK_DOWN) & 0x8000)
        {
            dx = 0;
            dy = 1;
        }

        x = x + dx;
        y = y + dy;

        if (x < 0 || x >= size || y < 0 || y >= size)
        {
            cout << "game over" << endl;
            break;
        }

        system("cls");
        cout << "x=" << x << " y=" << y << endl;
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
- `GetAsyncKeyState`로 방향이 눌렸는지 보고 `dx`/`dy`를 바꿉니다.
- `x`/`y`가 `0` 이상 `size` 미만이 아니면 `game over` 후 `break`합니다.
- 매 틱 화면을 지운 뒤 좌표를 출력하고 `Sleep`으로 속도를 늦춥니다.

### 27-3 테트리스 줄 삭제

테트리스도 **입력 → 갱신 → 출력** 루프는 같습니다. 차이는 갱신 규칙입니다. 블록을 내리고 고정한 뒤, 가로가 꽉 찬 줄을 **삭제**합니다. 입문에서는 낙하·회전 전체 대신 **보드에서 꽉 찬 줄 비우기**만 연습합니다.

보드를 **이차원 배열**로 두면 칸을 `board[행][열]`로 접근합니다. 값이 `1`이면 블록이 있는 칸, `0`이면 빈 칸이라고 약속합니다.

| 단계 | 할 일 |
|---|---|
| 입력 | 좌우·회전 등. 이 소절 예제에서는 생략 |
| 갱신 | 꽉 찬 줄 삭제 |
| 출력 | 보드 상태 확인 |

아래를 실행해 맨 아래 줄이 비고 `cleared=1`이 출력되는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

const int Rows = 3;
const int Cols = 4;

int ClearFullLines(int board[Rows][Cols])
{
    int cleared = 0;

    for (int r = Rows - 1; r >= 0; r--)
    {
        bool full = true;
        for (int c = 0; c < Cols; c++)
        {
            if (board[r][c] == 0)
            {
                full = false;
                break;
            }
        }

        if (full)
        {
            cleared++;
            for (int move = r; move > 0; move--)
            {
                for (int c = 0; c < Cols; c++)
                {
                    board[move][c] = board[move - 1][c];
                }
            }
            for (int c = 0; c < Cols; c++)
            {
                board[0][c] = 0;
            }
            r++;
        }
    }

    return cleared;
}

int main()
{
    int board[Rows][Cols] = {};
    for (int c = 0; c < Cols; c++)
    {
        board[2][c] = 1;
    }
    board[1][0] = 1;

    int n = ClearFullLines(board);
    cout << "cleared=" << n << endl;
    cout << "bottom="
         << board[2][0] << board[2][1] << board[2][2] << board[2][3]
         << endl;
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
- `ClearFullLines`는 아래 줄부터 검사해 꽉 찬 줄을 지우고 위를 한 칸씩 내립니다.
- 삭제 후 맨 아래는 `1000`처럼 한 칸만 블록이 남은 상태가 됩니다.

### 27-4 난수로 먹이 놓기

뱀 게임을 완성하려면 먹이가 매번 다른 자리에 나타나야 합니다. 실행할 때마다 달라지는 수를 **난수**라고 합니다. `<cstdlib>`의 **`rand()`**는 호출할 때마다 `0` 이상의 정수를 하나 돌려줍니다. 나머지 연산자로 범위를 줄여 씁니다. `rand() % 8`은 `0`부터 `7`까지, `rand() % 8 + 1`은 `1`부터 `8`까지입니다.

`rand()`만 쓰면 프로그램을 켤 때마다 같은 순서의 수가 나옵니다. 시작할 때 **`srand(시작값)`**을 한 번 호출해 순서를 바꿉니다. 시작값으로는 `<ctime>`의 `time(nullptr)`이 돌려주는 현재 시각을 넣는 것이 관례입니다.

| 사용형 | 의미 |
|---|---|
| `srand(static_cast<unsigned int>(time(nullptr)));` | 현재 시각으로 난수 순서를 정한다. `main` 시작에서 한 번만 |
| `rand() % n` | `0` 이상 `n` 미만의 정수 |
| `rand() % n + 1` | `1` 이상 `n` 이하의 정수 |

아래를 여러 번 실행해 매번 다른 좌표가 나오는지 확인해 보세요.

```cpp
#include <iostream>
#include <cstdlib>
#include <ctime>
using namespace std;

int main()
{
    srand(static_cast<unsigned int>(time(nullptr)));

    int x = rand() % 18 + 1;
    int y = rand() % 8 + 1;
    cout << "food at " << x << ", " << y << endl;
    return 0;
}
```

**예상 출력** — 숫자는 실행마다 다름

```text
food at 7, 3
```

**코드 해석**

- `srand`는 프로그램 시작에서 한 번만 호출합니다. 루프 안에서 매번 호출하면 같은 수가 반복됩니다.
- `x`는 `1`부터 `18`까지, `y`는 `1`부터 `8`까지 나옵니다.

### 27-5 뱀 게임 완성하기

27-2의 머리 이동에 몸통, 먹이, 점수를 더해 게임 하나를 완성합니다. 새로 정할 규칙은 네 가지입니다.

| 규칙 | 구현 방법 |
|---|---|
| 몸통이 머리를 따라온다 | 좌표를 `deque`에 담고, 매 틱 앞에 새 머리를 넣고 뒤에서 꼬리를 뺀다 |
| 먹이를 먹으면 길어진다 | 먹은 틱에는 꼬리를 빼지 않는다 |
| 벽이나 자기 몸에 닿으면 끝난다 | 새 머리 좌표가 벽이거나 `deque` 안에 이미 있으면 종료 |
| 반대 방향으로는 못 돈다 | 오른쪽으로 가는 중에는 왼쪽 키를 무시한다 |

몸통의 좌표는 Chapter 18에서 배운 `deque`에 담습니다. 앞과 뒤 양쪽에서 넣고 빼는 일이 매 틱 일어나므로 `deque`가 잘 맞습니다. 좌표 한 쌍은 C에서 배운 구조체로 묶습니다. C++에서는 `struct Point`로 정의한 뒤 `struct`를 다시 적지 않고 `Point`만으로 타입 이름을 씁니다.

화면은 매 틱 전부 지우지 않습니다. `system("cls")`를 매번 호출하면 화면이 깜빡입니다. 벽은 처음에 한 번만 그리고, 이후에는 **바뀐 칸만** 다시 찍습니다. 새 머리 칸에 `O`, 이전 머리 칸에 `o`, 빠진 꼬리 칸에 공백을 찍으면 됩니다.

아래를 실행해 방향키로 뱀을 움직이고, 먹이 `*`를 먹을 때마다 점수가 `10`씩 오르는지 확인해 보세요. 콘솔 창을 클릭해 포커스를 둔 뒤 조작합니다.

```cpp
#include <iostream>
#include <deque>
#include <cstdlib>
#include <ctime>
#include <windows.h>
using namespace std;

const int Width = 20;
const int Height = 10;

struct Point
{
    int x;
    int y;
};

void Put(int x, int y, char c)
{
    COORD pos = {static_cast<SHORT>(x), static_cast<SHORT>(y)};
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), pos);
    cout << c;
}

void DrawWalls()
{
    for (int x = 0; x < Width; x++)
    {
        Put(x, 0, '#');
        Put(x, Height - 1, '#');
    }
    for (int y = 0; y < Height; y++)
    {
        Put(0, y, '#');
        Put(Width - 1, y, '#');
    }
}

bool IsOnSnake(const deque<Point>& snake, int x, int y)
{
    for (const Point& part : snake)
    {
        if (part.x == x && part.y == y)
        {
            return true;
        }
    }
    return false;
}

Point MakeFood(const deque<Point>& snake)
{
    while (true)
    {
        int x = rand() % (Width - 2) + 1;
        int y = rand() % (Height - 2) + 1;
        if (!IsOnSnake(snake, x, y))
        {
            return Point{x, y};
        }
    }
}

int main()
{
    srand(static_cast<unsigned int>(time(nullptr)));
    system("cls");
    DrawWalls();

    deque<Point> snake;
    snake.push_back(Point{5, 5});
    snake.push_back(Point{4, 5});
    snake.push_back(Point{3, 5});
    for (const Point& part : snake)
    {
        Put(part.x, part.y, 'o');
    }

    Point food = MakeFood(snake);
    Put(food.x, food.y, '*');

    int dx = 1;
    int dy = 0;
    int score = 0;

    while (true)
    {
        if ((GetAsyncKeyState(VK_LEFT) & 0x8000) && dx != 1)
        {
            dx = -1;
            dy = 0;
        }
        else if ((GetAsyncKeyState(VK_RIGHT) & 0x8000) && dx != -1)
        {
            dx = 1;
            dy = 0;
        }
        else if ((GetAsyncKeyState(VK_UP) & 0x8000) && dy != 1)
        {
            dx = 0;
            dy = -1;
        }
        else if ((GetAsyncKeyState(VK_DOWN) & 0x8000) && dy != -1)
        {
            dx = 0;
            dy = 1;
        }

        Point head = snake.front();
        int nextX = head.x + dx;
        int nextY = head.y + dy;

        if (nextX <= 0 || nextX >= Width - 1 || nextY <= 0 || nextY >= Height - 1)
        {
            break;
        }
        if (IsOnSnake(snake, nextX, nextY))
        {
            break;
        }

        Put(head.x, head.y, 'o');
        snake.push_front(Point{nextX, nextY});
        Put(nextX, nextY, 'O');

        if (nextX == food.x && nextY == food.y)
        {
            score = score + 10;
            food = MakeFood(snake);
            Put(food.x, food.y, '*');
        }
        else
        {
            Point tail = snake.back();
            Put(tail.x, tail.y, ' ');
            snake.pop_back();
        }

        COORD scorePos = {0, static_cast<SHORT>(Height)};
        SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), scorePos);
        cout << "score=" << score << flush;
        Sleep(150);
    }

    COORD endPos = {0, static_cast<SHORT>(Height + 1)};
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), endPos);
    cout << "game over" << endl;
    return 0;
}
```

**예상 출력** (벽에 부딪힌 뒤 화면 아래쪽. 점수는 플레이에 따라 다름)

```text
score=30
game over
```

**코드 해석**

- `Put`은 27-1의 커서 이동과 글자 출력을 한 함수로 묶은 것입니다. 화면의 한 칸만 바꿉니다.
- `Point{5, 5}`는 멤버 `x`, `y`에 순서대로 값을 넣어 `Point` 하나를 만듭니다.
- `IsOnSnake`는 좌표가 뱀의 몸 위에 있는지 검사합니다. 자기 몸 충돌 검사와 먹이 위치 정하기 두 곳에서 씁니다. `for (const Point& part : snake)`는 원소를 복사하지 않고 읽기 전용으로 하나씩 꺼내는 범위 기반 `for`입니다.
- `MakeFood`는 뱀의 몸과 겹치지 않는 좌표가 나올 때까지 난수를 다시 뽑습니다.
- 방향 입력의 `dx != 1` 같은 조건이 반대 방향 전환을 막습니다.
- `snake.push_front`로 새 머리를 넣습니다. 먹이를 먹었으면 꼬리를 그대로 두어 길이가 하나 늘고, 아니면 `pop_back`으로 꼬리를 빼 길이를 유지합니다.
- `cout << flush`는 줄바꿈 없이도 지금까지 보낸 글자를 바로 화면에 내보냅니다.
- 벽은 좌표 `0`과 `Width - 1`, `Height - 1`에 있으므로, 새 머리가 그 좌표에 닿으면 `break`로 루프를 끝냅니다.

Visual Studio 프로젝트에서는 그대로 빌드됩니다. `cl`로 직접 컴파일할 때는 `cl /EHsc main.cpp user32.lib`처럼 `user32.lib`를 함께 적어야 `GetAsyncKeyState`가 연결됩니다.

이 장에서는 콘솔 제어, 뱀 게임 완성, 테트리스 줄 삭제 핵심까지입니다. 테트리스는 27-3의 줄 삭제에 블록 낙하와 회전을 더하면 같은 루프 구조로 완성할 수 있습니다.

### 연습문제

**문제 1**

- 문제: `MoveTo`, `SetColor`, `Clear`를 포함한 유틸 클래스를 만들고 데모하세요.
- 입력: 없음
- 출력:
  ```text
  ok
  ```
- 조건: `static` 멤버. 특정 좌표에 색 글자를 쓴 뒤 `Clear`, 마지막에 `ok` 출력. 24장처럼 `ConsoleUtil.h` / `ConsoleUtil.cpp`로 나누고, 25장처럼 CMake 프로젝트로 빌드

**문제 2**

- 문제: 뱀 머리 1칸 이동과 벽 충돌 종료를 구현하세요.
- 입력: 방향키. 콘솔 포커스 필요
- 출력: 매 틱 `x=… y=…` 형태, 벽 밖이면 마지막에
  ```text
  game over
  ```
- 조건: `while` 루프 + `Sleep` + 키 입력. 벽 범위는 본문처럼 `0..9` 또는 스스로 정한 크기

**문제 3**

- 문제: 가로 4칸 보드에서 꽉 찬 줄을 삭제하는 함수를 작성하세요.
- 입력: 없음. 본문처럼 맨 아래 줄이 모두 `1`인 이차원 배열을 만들어 호출
- 출력:
  ```text
  cleared=1
  ```
  추가로 삭제 후 그 줄이 비었거나 위 블록이 내려온 상태를 숫자로 확인해도 됨
- 조건: 아래 줄부터 검사

**문제 4**

- 문제: 27-5의 뱀 게임에 기능을 하나 이상 더해 자신만의 버전을 완성하세요.
- 입력: 방향키
- 출력: 게임이 끝나면 마지막에 최종 점수와 `game over`
- 조건: 다음 중 하나 이상을 구현. 점수가 `50` 오를 때마다 `Sleep` 시간을 줄여 빨라지게 하기, 게임이 끝난 뒤 `R` 키로 다시 시작하기, 최고 점수를 12장의 파일 입출력으로 저장하고 다음 실행에 보여 주기

### 정답 포인트

- 문제 1: 본문 `ConsoleUtil`과 같은 래퍼 + `main`에서 `ok` 출력
- 문제 2: 위치 갱신 후 범위 밖이면 `game over` 후 `break`
- 문제 3: 줄이 모두 `1`이면 위 행을 복사해 내리고 맨 위를 `0`으로. `cleared` 증가
- 문제 4: 속도는 `Sleep`에 넘기는 값을 변수로 빼서 점수에 따라 줄임. 다시 시작은 게임 한 판을 함수로 묶고 바깥 루프에서 반복 호출. 최고 점수는 시작할 때 파일에서 읽고 끝날 때 더 크면 덮어씀
- 공통: 콘솔 API는 유틸로, 뱀/테트리스는 루프는 같고 갱신 규칙만 다름

---

[상위 문서로 돌아가기](./README.md)
