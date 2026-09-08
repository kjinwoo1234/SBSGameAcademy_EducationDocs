# Chapter 14 윈도우와 데이터 연결

## 학습 목표

- `CreateWindow`의 `lpParam`이 생성 시 데이터를 넘기는 자리임을 설명할 수 있다.
- `SetWindowLongPtr` / `GetWindowLongPtr`와 `GWLP_USERDATA`로 창마다 데이터를 붙일 수 있다.
- C++ 객체 포인터를 창에 연결하는 이유를 말할 수 있다.

## 본문

### 14-1 전역 변수의 한계

지금까지는 `static int g_x`처럼 **전역·정적 변수**에 게임 상태를 두었습니다. 창이 하나일 때는 편하지만, 창이 여러 개이거나 클래스로 상태를 묶고 싶을 때는 창마다 **자기 데이터**가 필요합니다.

```text
HWND
 ↓
각 윈도우마다 자기 데이터를 가질 수 있음
```

### 14-2 GWLP_USERDATA

Windows는 창에 포인터 크기 값을 하나 붙여 둘 수 있는 자리를 제공합니다. **`GWLP_USERDATA`**입니다.

```cpp
struct GameState
{
    int ballX;
    int ballY;
};

// 생성 직후 또는 WM_CREATE에서
GameState* state = new GameState{ 200, 120 };
SetWindowLongPtr(hwnd, GWLP_USERDATA, (LONG_PTR)state);

// 나중에
GameState* s = (GameState*)GetWindowLongPtr(hwnd, GWLP_USERDATA);
```

| API | 역할 |
|-----|------|
| `SetWindowLongPtr` | 창에 값 저장 |
| `GetWindowLongPtr` | 창에서 값 읽기 |
| `GWLP_USERDATA` | 사용자 데이터 슬롯 |

`WM_DESTROY`에서 `delete`로 해제하는 것을 잊지 마세요.

### 14-3 lpParam과 WM_CREATE

`CreateWindow`의 마지막 인자 **`lpParam`**은 생성 시 넘기는 포인터입니다. `WM_CREATE`의 `lParam`을 `CREATESTRUCT*`로 받아 `lpCreateParams`로 읽을 수 있습니다.

```cpp
HWND hwnd = CreateWindow(
    L"Class", L"Title", WS_OVERLAPPEDWINDOW,
    CW_USEDEFAULT, CW_USEDEFAULT, 640, 480,
    NULL, NULL, hInstance, state);  // lpParam

case WM_CREATE:
{
    CREATESTRUCT* cs = (CREATESTRUCT*)lParam;
    GameState* s = (GameState*)cs->lpCreateParams;
    SetWindowLongPtr(hwnd, GWLP_USERDATA, (LONG_PTR)s);
    return 0;
}
```

C++에서 `WndProc`을 멤버 함수처럼 쓰려면, 정적 `WndProc`이 `GWLP_USERDATA`에 넣어 둔 `this`를 꺼내 멤버를 호출하는 패턴이 자주 나옵니다. 이 장에서는 **포인터를 창에 붙인다**는 개념만 확실히 하면 됩니다.

### 연습문제

**문제 1**

- 문제: 창에 사용자 포인터를 붙일 때 쓰는 인덱스 상수 이름을 쓰세요.
- 입력: 없음
- 출력: 상수 이름
- 조건: `14-2`

**문제 2**

- 문제: `CreateWindow`에서 생성 데이터를 넘기는 마지막 인자 관례 이름을 쓰세요.
- 입력: 없음
- 출력: 이름
- 조건: `14-3`

**문제 3**

- 문제: `GameState`를 `new`로 만들어 창에 붙이고, `WM_TIMER`에서 `GetWindowLongPtr`로 읽어 공을 움직이게 하세요. 종료 시 `delete`하세요.
- 입력: 없음
- 출력: 전역 `g_x` 없이 동작
- 조건: `GWLP_USERDATA` 사용

### 정답 포인트

- 문제 1: `GWLP_USERDATA`
- 문제 2: `lpParam`
- 문제 3: CREATE에서 set, TIMER/PAINT에서 get, DESTROY에서 delete

---

[상위 문서로 돌아가기](./README.md)
