# Chapter 23 콘솔 제어로 뱀/테트리스 만들기

## 학습 목표
- C# 콘솔에서 커서 이동/텍스트 색상/화면 지우기를 제어할 수 있다.
- 뱀게임과 테트리스의 공통 게임 루프를 구현할 수 있다.

## 본문

### 23-1 콘솔 제어 함수

C# 콘솔은 **좌표·색·지우기**만으로도 간단한 게임 화면을 만들 수 있습니다. **`Console.SetCursorPosition(x, y)`**로 출력 위치를 옮기고, **`Console.ForegroundColor`**로 글자 색을 바꾸며, **`Console.Clear()`**로 화면을 지웁니다. 게임 코드에 API를 흩뿌리지 말고 유틸로 감싸면 뱀·테트리스가 같은 도구를 씁니다.

| 기능 | C# 콘솔 함수 | 용도 |
|---|---|---|
| 커서 이동 | `Console.SetCursorPosition(x, y)` | 좌표 출력 |
| 텍스트 색상 | `Console.ForegroundColor` | 상태 강조 |
| 화면 지우기 | `Console.Clear()` | 화면 초기화 |

| 용어 | 의미 |
|---|---|
| 게임 루프(game loop) | 입력 처리 → 상태 갱신 → 출력 반복 구조 |
| 상태 갱신(update) | 현재 프레임에서 게임 데이터(좌표/점수)를 계산하는 단계 |

아래 코드를 실행해 좌표 `(5, 2)`에 초록 글자가 잠깐 보인 뒤 화면이 지워지고 `cleared`가 출력되는지 확인해 보세요.

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
- 실제 게임에서는 Clear 대신 부분만 다시 그려 깜빡임을 줄이기도 합니다.

### 23-2 뱀게임 루프 설계

뱀은 **매 틱**마다 같은 뼈대를 돕니다. 방향을 읽고 → 머리 좌표를 한 칸 옮기고 → 벽·몸통 충돌이면 종료 → 먹이면 길이·점수 증가 → 화면을 그립니다. `Thread.Sleep`으로 틱 간격을 둡니다.

| 단계 | 할 일 |
|---|---|
| 입력 | 방향키로 다음 방향 결정 |
| 갱신 | 머리 이동, 충돌·먹이 처리 |
| 출력 | 몸·먹이를 콘솔에 그림 |
| 대기 | `Thread.Sleep`으로 속도 조절 |

### 23-3 테트리스 루프 설계

테트리스도 **입력 → 갱신 → 출력** 루프는 같습니다. 차이는 갱신 규칙입니다. 일정 시간마다 블록을 내리고, 아래가 막히면 보드에 **고정**, 가로가 꽉 찬 줄을 **삭제**한 뒤 점수를 더합니다.

| 단계 | 할 일 |
|---|---|
| 입력 | 좌우·회전·하드드롭 |
| 갱신 | 낙하, 충돌, 고정, 줄 삭제 |
| 출력 | 보드·현재 블록 그리기 |

### 연습문제

**문제 1**
- 문제: `MoveTo`, `SetColor`, `Clear`를 포함한 유틸 클래스를 구현하세요.
- 입력: 없음
- 출력: 특정 좌표에 색 글자 표시 후 Clear, 마지막에 `ok` 한 줄
- 조건: `static` 메서드

**문제 2**
- 문제: 뱀 머리 1칸 이동과 벽 충돌 종료를 구현하세요.
- 입력: 방향키(또는 문자 입력으로 단순화)
- 출력: 매 틱 머리 좌표(예: `x=3 y=1`), 벽이면 `game over`
- 조건: 루프 + `Thread.Sleep`

**문제 3**
- 문제: 꽉 찬 줄을 감지해 삭제하는 함수를 작성하세요.
- 입력: 없음(예: 가로 4칸 보드에서 한 줄이 모두 채워진 `bool[,]` 또는 `int[,]`)
- 출력: 삭제 후 그 줄이 비었음(또는 삭제 줄 수 `1`)
- 조건: 아래 줄부터 검사

### 정답 포인트

- 문제 1: 본문 `ConsoleUtil`과 같은 래퍼 + `Main` 데모
- 문제 2: 위치 갱신 후 `x`/`y`가 범위 밖이면 break
- 문제 3: 줄이 모두 채워졌으면 위를 한 칸씩 내리고 맨 위를 빈 줄로
- 공통: 콘솔 API는 유틸로, 뱀/테트리스는 루프는 같고 갱신 규칙만 다름

---

[상위 문서로 돌아가기](./README.md)
