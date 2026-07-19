# Chapter 23 콘솔 제어로 뱀/테트리스 만들기

## 학습 목표
- `SetCursorPosition`·색·`Clear`로 콘솔 화면을 제어할 수 있다.
- 입력 → 갱신 → 출력 → 대기 게임 루프로 뱀 머리 이동·벽 충돌을 구현할 수 있다.
- 이차원 배열 보드에서 꽉 찬 줄을 찾아 비울 수 있다.

## 본문

### 23-1 콘솔 제어 함수

C# 콘솔은 **좌표·색·지우기**만으로도 간단한 게임 화면을 만들 수 있습니다. **`Console.SetCursorPosition(x, y)`**는 다음에 쓸 글자의 칸을 `x`열·`y`행으로 옮깁니다. 왼쪽 위가 `(0, 0)`입니다. **`Console.ForegroundColor`**에 **`ConsoleColor`** 값을 넣으면 글자 색이 바뀝니다. **`Console.Clear()`**는 화면을 비웁니다. **`Thread.Sleep(밀리초)`**는 그 시간만큼 잠시 멈춥니다. `using System.Threading;`이 필요합니다.

게임 코드에 API를 흩뿌리지 말고 유틸 클래스로 감싸면 뱀·테트리스가 같은 도구를 씁니다.

| 기능 | 사용형 | 용도 |
|---|---|---|
| 커서 이동 | `Console.SetCursorPosition(x, y)` | 좌표에 출력 |
| 글자 색 | `Console.ForegroundColor = ConsoleColor.Green` | 상태 강조 |
| 화면 지우기 | `Console.Clear()` | 화면 초기화 |
| 대기 | `Thread.Sleep(500)` | 틱 속도 조절 |

| 용어 | 의미 |
|---|---|
| 게임 루프 | 입력 → 상태 갱신 → 출력 → 대기를 반복하는 구조 |
| 틱 | 루프가 한 바퀴 도는 한 번의 갱신 단위 |

아래를 실행해 좌표 `(5, 2)`에 초록 글자가 잠깐 보인 뒤 화면이 지워지고 `cleared`가 출력되는지 확인해 보세요.

```csharp
using System;
using System.Threading;

class ConsoleUtil
{
    public static void MoveTo(int x, int y)
    {
        Console.SetCursorPosition(x, y);
    }

    public static void SetColor(ConsoleColor color)
    {
        Console.ForegroundColor = color;
    }

    public static void Clear()
    {
        Console.Clear();
    }
}

class Program
{
    static void Main()
    {
        ConsoleUtil.Clear();
        ConsoleUtil.SetColor(ConsoleColor.Green);
        ConsoleUtil.MoveTo(5, 2);
        Console.Write("snake");
        Thread.Sleep(500);
        ConsoleUtil.Clear();
        Console.ResetColor();
        Console.WriteLine("cleared");
    }
}
```

**예상 출력** (Clear 이후 최종 화면)

```text
cleared
```

**코드 해석**
- 유틸이 커서·색·Clear를 감쌉니다.
- `(5, 2)`에 `snake`를 그린 뒤 지우고 `cleared`를 남깁니다.
- `ResetColor`로 글자 색을 기본값으로 되돌립니다.

### 23-2 뱀게임 루프 설계

뱀과 테트리스는 모두 **게임 루프**를 돕니다. 뱀은 매 틱마다 방향을 읽고, 머리 좌표를 한 칸 옮기고, 벽이면 종료하고, 화면을 그린 뒤 `Thread.Sleep`으로 속도를 조절합니다. 몸통·먹이·점수까지 넣은 완전체는 이 장 범위 밖입니다. 여기서는 **머리 한 칸 + 벽 충돌**만 따라 합니다.

**`Console.ReadKey(true)`**는 키 하나를 읽습니다. `true`는 누른 키가 화면에 에코되지 않게 합니다. **`Key`** 속성으로 방향키를 구분합니다. 루프 안에서 매번 기다리면 멈춘 느낌이 나므로, 입문 예제는 **키가 있을 때만** 읽고 없으면 이전 방향을 유지합니다. **`Console.KeyAvailable`**이 참일 때 키가 대기 중이라는 뜻입니다.

| 단계 | 할 일 |
|---|---|
| 입력 | 방향키로 `dx`/`dy` 결정 |
| 갱신 | 머리 `x`/`y` 이동, 벽이면 종료 |
| 출력 | 좌표 출력 |
| 대기 | `Thread.Sleep` |

아래를 실행해 방향키로 머리를 옮기고, 벽 `0..9` 밖으로 나가면 `game over`가 나오는지 확인해 보세요. 콘솔 창을 클릭해 포커스를 둔 뒤 조작합니다.

```csharp
using System;
using System.Threading;

class Program
{
    static void Main()
    {
        int x = 5;
        int y = 5;
        int dx = 1;
        int dy = 0;
        int size = 10;

        while (true)
        {
            if (Console.KeyAvailable)
            {
                ConsoleKey key = Console.ReadKey(true).Key;
                if (key == ConsoleKey.LeftArrow) { dx = -1; dy = 0; }
                if (key == ConsoleKey.RightArrow) { dx = 1; dy = 0; }
                if (key == ConsoleKey.UpArrow) { dx = 0; dy = -1; }
                if (key == ConsoleKey.DownArrow) { dx = 0; dy = 1; }
            }

            x = x + dx;
            y = y + dy;

            if (x < 0 || x >= size || y < 0 || y >= size)
            {
                Console.WriteLine("game over");
                break;
            }

            Console.Clear();
            Console.WriteLine($"x={x} y={y}");
            Thread.Sleep(200);
        }
    }
}
```

**예상 출력** (벽 밖으로 나간 뒤 마지막 줄)

```text
game over
```

**코드 해석**
- `dx`/`dy`가 한 틱에 머리가 움직이는 칸 수입니다. 처음에는 오른쪽으로 `1`칸입니다.
- `KeyAvailable`이 참일 때만 `ReadKey`로 방향을 바꿉니다.
- `x`/`y`가 `0` 이상 `size` 미만이 아니면 `game over` 후 `break`합니다.
- 매 틱 `Clear` 후 좌표를 출력하고 `Sleep`으로 속도를 늦춥니다.

### 23-3 테트리스 줄 삭제

테트리스도 **입력 → 갱신 → 출력** 루프는 같습니다. 차이는 갱신 규칙입니다. 블록을 내리고 고정한 뒤, 가로가 꽉 찬 줄을 **삭제**합니다. 입문에서는 낙하·회전 전체 대신 **보드에서 꽉 찬 줄 비우기**만 연습합니다.

보드를 **이차원 배열**로 두면 칸을 `board[행, 열]`로 접근합니다. `int[,]`는 정수 칸을 행·열로 나란히 둔 배열입니다. `new int[행개수, 열개수]`로 만듭니다. 값이 `1`이면 블록이 있는 칸, `0`이면 빈 칸이라고 약속합니다.

| 단계 | 할 일 |
|---|---|
| 입력 | 좌우·회전 등. 이 소절 예제에서는 생략 |
| 갱신 | 꽉 찬 줄 삭제 |
| 출력 | 보드 상태 확인 |

아래를 실행해 맨 아래 줄이 비고 `cleared=1`이 출력되는지 확인해 보세요.

```csharp
using System;

class Program
{
    static int ClearFullLines(int[,] board)
    {
        int rows = board.GetLength(0);
        int cols = board.GetLength(1);
        int cleared = 0;

        for (int r = rows - 1; r >= 0; r--)
        {
            bool full = true;
            for (int c = 0; c < cols; c++)
            {
                if (board[r, c] == 0)
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
                    for (int c = 0; c < cols; c++)
                    {
                        board[move, c] = board[move - 1, c];
                    }
                }
                for (int c = 0; c < cols; c++)
                {
                    board[0, c] = 0;
                }
                r++;
            }
        }

        return cleared;
    }

    static void Main()
    {
        int[,] board = new int[3, 4];
        for (int c = 0; c < 4; c++)
        {
            board[2, c] = 1;
        }
        board[1, 0] = 1;

        int n = ClearFullLines(board);
        Console.WriteLine($"cleared={n}");
        Console.WriteLine($"bottom={board[2, 0]}{board[2, 1]}{board[2, 2]}{board[2, 3]}");
    }
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
- `GetLength(0)`은 행 개수, `GetLength(1)`은 열 개수입니다.
- 삭제 후 맨 아래는 `1000`처럼 한 칸만 블록이 남은 상태가 됩니다.

이 장에서는 콘솔 제어, 뱀 머리 루프, 테트리스 줄 삭제 핵심까지입니다. 완성 게임·그래픽 엔진은 다음 단계로 미룹니다.

### 연습문제

**문제 1**
- 문제: `MoveTo`, `SetColor`, `Clear`를 포함한 유틸 클래스를 만들고 데모하세요.
- 입력: 없음
- 출력:
  ```text
  ok
  ```
- 조건: `static` 메서드. 특정 좌표에 색 글자를 쓴 뒤 `Clear`, 마지막에 `ok` 출력

**문제 2**
- 문제: 뱀 머리 1칸 이동과 벽 충돌 종료를 구현하세요.
- 입력: 방향키. 콘솔 포커스 필요
- 출력: 매 틱 `x=… y=…` 형태, 벽 밖이면 마지막에
  ```text
  game over
  ```
- 조건: `while` 루프 + `Thread.Sleep` + `KeyAvailable`/`ReadKey`. 벽 범위는 본문처럼 `0..9` 또는 스스로 정한 크기

**문제 3**
- 문제: 가로 4칸 보드에서 꽉 찬 줄을 삭제하는 함수를 작성하세요.
- 입력: 없음. 본문처럼 맨 아래 줄이 모두 `1`인 `int[,]`를 만들어 호출
- 출력:
  ```text
  cleared=1
  ```
  추가로 삭제 후 그 줄이 비었거나 위 블록이 내려온 상태를 숫자로 확인해도 됨
- 조건: 아래 줄부터 검사. `GetLength`로 크기 읽기

### 정답 포인트

- 문제 1: 본문 `ConsoleUtil`과 같은 래퍼 + `Main`에서 `ok` 출력
- 문제 2: 위치 갱신 후 범위 밖이면 `game over` 후 `break`
- 문제 3: 줄이 모두 `1`이면 위 행을 복사해 내리고 맨 위를 `0`으로. `cleared` 증가
- 공통: 콘솔 API는 유틸로, 뱀/테트리스는 루프는 같고 갱신 규칙만 다름

---

[상위 문서로 돌아가기](./README.md)
