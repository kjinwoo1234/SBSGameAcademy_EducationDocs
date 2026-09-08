# Chapter 09 마우스 입력

## 학습 목표

- `WM_LBUTTONDOWN` / `WM_MOUSEMOVE`에서 좌표를 읽을 수 있다.
- `LOWORD` / `HIWORD`로 `lParam`에서 x, y를 꺼낼 수 있다.
- 클릭 위치에 도형을 그리는 프로그램을 작성할 수 있다.

## 본문

### 09-1 마우스 메시지와 lParam

마우스 메시지에서는 보통 이렇게 나눕니다.

```text
wParam  → 버튼·Shift 등 플래그
lParam  → 좌표. 하위 워드 x, 상위 워드 y
```

좌표를 꺼낼 때는 매크로를 씁니다.

```cpp
int x = LOWORD(lParam);
int y = HIWORD(lParam);
```

| 메시지 | 용도 |
|--------|------|
| `WM_LBUTTONDOWN` | 왼쪽 버튼 누름 |
| `WM_LBUTTONUP` | 왼쪽 버튼 뗌 |
| `WM_MOUSEMOVE` | 포인터 이동 |
| `WM_RBUTTONDOWN` | 오른쪽 버튼 누름 |

### 09-2 클릭한 곳에 원 그리기

마지막 클릭 좌표를 저장하고 `WM_PAINT`에서 원을 그립니다.

```cpp
static int g_mx = -1;
static int g_my = -1;

case WM_LBUTTONDOWN:
    g_mx = LOWORD(lParam);
    g_my = HIWORD(lParam);
    InvalidateRect(hwnd, NULL, TRUE);
    return 0;

case WM_PAINT:
{
    PAINTSTRUCT ps;
    HDC hdc = BeginPaint(hwnd, &ps);
    if (g_mx >= 0)
    {
        Ellipse(hdc, g_mx - 20, g_my - 20, g_mx + 20, g_my + 20);
    }
    EndPaint(hwnd, &ps);
    return 0;
}
```

**예상 결과**

```text
창을 왼쪽 클릭할 때마다 그 위치에 원이 그려짐
```

**코드 해석**

- `LOWORD`/`HIWORD`로 클라이언트 좌표를 얻습니다.
- 그리기는 여전히 `WM_PAINT`에서만 합니다. 클릭 핸들러에서 바로 `Ellipse`만 호출하면, 창이 가려졌다가 돌아올 때 그림이 사라질 수 있습니다.

드래그 선 그리기는 `WM_LBUTTONDOWN`에서 드래그 시작, `WM_MOUSEMOVE`에서 선 추가, `WM_LBUTTONUP`에서 종료 패턴을 씁니다. **그림판 도전 장**에서 이어서 연습합니다.

### 연습문제

**문제 1**

- 문제: 마우스 x 좌표를 `lParam`에서 꺼내는 매크로 이름을 쓰세요.
- 입력: 없음
- 출력: 매크로 이름
- 조건: `09-1`

**문제 2**

- 문제: 클릭 처리에서 바로 그리지 않고 `InvalidateRect`를 쓰는 이유를 한 문장으로 쓰세요.
- 입력: 없음
- 출력: 한 문장
- 조건: `09-2` 코드 해석

**문제 3**

- 문제: 클릭 위치에 원을 그리는 프로그램을 실행해 확인하세요.
- 입력: 왼쪽 클릭
- 출력: 원
- 조건: `WM_PAINT`에서 그리기

### 정답 포인트

- 문제 1: `LOWORD`
- 문제 2: 다시 그릴 때 그림이 남도록 / 페인트에서 일괄 그리기
- 문제 3: down → 좌표 저장 → invalidate → paint

---

[상위 문서로 돌아가기](./README.md)
