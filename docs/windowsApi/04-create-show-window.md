# Chapter 04 CreateWindow와 창 표시

## 학습 목표

- `CreateWindow`로 실제 창을 만들 수 있다.
- `ShowWindow`와 `UpdateWindow`의 역할을 구분할 수 있다.
- `HWND`가 창을 가리키는 핸들임을 설명할 수 있다.

## 본문

### 04-1 CreateWindow

**`CreateWindow`**는 등록해 둔 클래스 이름으로 **실제 창**을 만듭니다. 성공하면 **`HWND`**를 돌려줍니다. 이후 그리기·타이머·자식 컨트롤에 이 핸들을 넘깁니다.

자주 보는 인자만 짚습니다.

| 인자 | 뜻 |
|------|-----|
| 클래스 이름 | `RegisterClass` 때 쓴 `lpszClassName`과 같아야 함 |
| 창 제목 | 제목 표시줄 글자 |
| `dwStyle` | 테두리·최소화 버튼 등. 입문은 `WS_OVERLAPPEDWINDOW` |
| 위치·크기 | x, y, width, height. `CW_USEDEFAULT`로 기본값 가능 |
| `hWndParent` | 부모 창. 최상위 창은 `NULL` |
| `hMenu` | 메뉴 또는 자식 컨트롤 ID. 입문은 `NULL` 가능 |
| `hInstance` | `WinMain`의 인스턴스 |
| `lpParam` | 생성 시 넘길 사용자 데이터. 입문은 `NULL`, 심화는 뒤 장 |

### 04-2 ShowWindow와 UpdateWindow

창을 만들기만 하고 끝내면 화면에 안 보일 수 있습니다.

- **`ShowWindow(hwnd, nCmdShow)`** — 창을 보이게 합니다. `nCmdShow`는 `WinMain`에서 받은 값을 그대로 넘기는 경우가 많습니다.
- **`UpdateWindow(hwnd)`** — 곧바로 그려야 할 영역을 갱신해 `WM_PAINT`가 나가게 합니다.

아래는 등록 → 생성 → 표시까지입니다. 메시지 루프는 아직 없어서, 창이 뜬 뒤 바로 종료될 수 있습니다. **루프는 다음 장**에서 붙입니다.

```cpp
#include <windows.h>

LRESULT CALLBACK WndProc(HWND hwnd, UINT iMsg, WPARAM wParam, LPARAM lParam)
{
    return DefWindowProc(hwnd, iMsg, wParam, lParam);
}

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE, LPSTR, int nCmdShow)
{
    WNDCLASS wc = {};
    wc.lpfnWndProc = WndProc;
    wc.hInstance = hInstance;
    wc.hCursor = LoadCursor(NULL, IDC_ARROW);
    wc.hbrBackground = (HBRUSH)(COLOR_WINDOW + 1);
    wc.lpszClassName = L"MyFirstClass";
    RegisterClass(&wc);

    HWND hwnd = CreateWindow(
        L"MyFirstClass",
        L"첫 창",
        WS_OVERLAPPEDWINDOW,
        CW_USEDEFAULT, CW_USEDEFAULT,
        640, 480,
        NULL, NULL, hInstance, NULL);

    if (!hwnd)
    {
        MessageBox(NULL, L"CreateWindow 실패", L"오류", MB_OK);
        return 0;
    }

    ShowWindow(hwnd, nCmdShow);
    UpdateWindow(hwnd);

    MessageBox(NULL, L"창을 만들었습니다. 확인 후 종료합니다.", L"Chapter 04", MB_OK);
    return 0;
}
```

**예상 결과**

```text
빈 창이 잠깐 보이거나, 메시지 상자 확인 후 프로그램 종료
```

**코드 해석**

- `CreateWindow` 반환값이 `NULL`이면 생성 실패입니다.
- `ShowWindow` / `UpdateWindow`로 표시를 요청합니다.
- 메시지 루프가 없으면 이벤트를 계속 받을 수 없습니다. **다음 장**에서 루프를 넣습니다.

### 연습문제

**문제 1**

- 문제: 창을 가리키는 핸들 타입 이름을 쓰세요.
- 입력: 없음
- 출력: 타입 이름
- 조건: `04-1`

**문제 2**

- 문제: 입문에서 자주 쓰는 `dwStyle` 값을 쓰세요.
- 입력: 없음
- 출력: 상수 이름
- 조건: `04-1` 표

**문제 3**

- 문제: 창 제목을 `"내 창"`으로 바꿔 실행하세요.
- 입력: 없음
- 출력: 제목 표시줄에 `내 창`
- 조건: `CreateWindow` 두 번째 인자만 변경

### 정답 포인트

- 문제 1: `HWND`
- 문제 2: `WS_OVERLAPPEDWINDOW`
- 문제 3: 제목 문자열만 교체

---

[상위 문서로 돌아가기](./README.md)
