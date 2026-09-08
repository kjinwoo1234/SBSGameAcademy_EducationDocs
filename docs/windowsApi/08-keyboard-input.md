# Chapter 08 키보드 입력

## 학습 목표

- `WM_KEYDOWN` / `WM_KEYUP` / `WM_CHAR`의 차이를 구분할 수 있다.
- `wParam`으로 가상 키 코드를 읽어 분기할 수 있다.
- 방향키에 따라 좌표를 바꾸고 `InvalidateRect`로 다시 그릴 수 있다.

## 본문

### 08-1 키 메시지

| 메시지 | 언제 |
|--------|------|
| `WM_KEYDOWN` | 키를 누르는 순간. 게임 이동에 자주 씀 |
| `WM_KEYUP` | 키를 떼는 순간 |
| `WM_CHAR` | 문자로 변환된 입력. 텍스트 입력에 유리 |

방향키·게임 조작은 보통 **`WM_KEYDOWN`**에서 `wParam`을 봅니다.

```text
wParam  → 어떤 키인가?  (VK_LEFT, VK_UP, 'A' 등)
lParam  → 반복 횟수 등 추가 비트 (입문에선 나중에)
```

### 08-2 방향키로 네모 움직이기

전역 또는 정적 변수에 좌표를 두고, 키를 누를 때마다 옮긴 뒤 다시 그립니다.

```cpp
static int g_x = 200;
static int g_y = 150;

// WndProc 안
case WM_KEYDOWN:
    if (wParam == VK_LEFT)  g_x -= 10;
    if (wParam == VK_RIGHT) g_x += 10;
    if (wParam == VK_UP)    g_y -= 10;
    if (wParam == VK_DOWN)  g_y += 10;
    InvalidateRect(hwnd, NULL, TRUE);
    return 0;

case WM_PAINT:
{
    PAINTSTRUCT ps;
    HDC hdc = BeginPaint(hwnd, &ps);
    Rectangle(hdc, g_x, g_y, g_x + 40, g_y + 40);
    EndPaint(hwnd, &ps);
    return 0;
}
```

**예상 결과**

```text
방향키를 누르면 네모가 이동하고 화면이 갱신됨
```

**코드 해석**

- `VK_LEFT` 등은 Windows가 정한 **가상 키 코드**입니다.
- `InvalidateRect`가 `WM_PAINT`를 유도합니다.
- 키가 길게 눌리면 `WM_KEYDOWN`이 반복될 수 있습니다. 입문에서는 그 동작으로도 충분합니다.

문자 입력이 필요하면 `WM_CHAR`의 `wParam`을 `wchar_t`로 읽어 글자를 붙입니다. 이동·점프 같은 게임 입력은 `WM_KEYDOWN`이 더 직관적입니다.

### 연습문제

**문제 1**

- 문제: 게임에서 “왼쪽 키를 눌렀다”를 받을 때 쓰기 좋은 메시지 이름을 쓰세요.
- 입력: 없음
- 출력: 메시지 이름
- 조건: `08-1`

**문제 2**

- 문제: 위쪽 방향키의 가상 키 상수 이름을 쓰세요.
- 입력: 없음
- 출력: 상수 이름
- 조건: 본문 예제

**문제 3**

- 문제: 방향키로 네모를 움직이는 프로그램을 실행해 확인하세요.
- 입력: 방향키
- 출력: 네모 이동
- 조건: `InvalidateRect` 사용

### 정답 포인트

- 문제 1: `WM_KEYDOWN`
- 문제 2: `VK_UP`
- 문제 3: 키 → 좌표 변경 → 무효화 → 페인트

---

[상위 문서로 돌아가기](./README.md)
