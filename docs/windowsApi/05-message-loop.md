# Chapter 05 메시지 루프

## 학습 목표

- 메시지 루프가 왜 필요한지 말할 수 있다.
- `GetMessage` → `TranslateMessage` → `DispatchMessage` 순서를 설명할 수 있다.
- 창이 닫힐 때까지 루프가 도는 프로그램을 작성할 수 있다.

## 본문

### 05-1 메시지가 무엇인가

Windows는 “키가 눌렸다”, “창을 다시 그려라”, “닫기 버튼이 눌렸다” 같은 일을 **메시지**로 전달합니다.  
프로그램은 메시지를 **꺼내서** 처리 함수(`WndProc`)에 **보내야** 합니다. 그 반복이 **메시지 루프**입니다.

루프가 없으면 창을 만들어도 입력을 계속 받을 수 없고, 곧바로 종료되기 쉽습니다.

```text
GetMessage()        메시지 꺼내기
    ↓
TranslateMessage()  키보드 관련 변환(문자 메시지 생성 등)
    ↓
DispatchMessage()   WndProc으로 전달
    ↓
(다시 GetMessage)
```

| 함수 | 역할 |
|------|------|
| `GetMessage` | 큐에서 메시지를 가져옴. `WM_QUIT`이면 0을 반환해 루프 종료 |
| `TranslateMessage` | 키 입력을 `WM_CHAR` 등으로 변환하는 데 관여 |
| `DispatchMessage` | 해당 창의 `WndProc` 호출 |

### 05-2 루프 붙이기

`MSG` 구조체에 메시지를 담아 루프를 돕니다. `GetMessage`가 0을 반환하면 종료합니다.

아래를 실행해 창을 띄운 뒤, 닫기 버튼으로 프로그램이 끝나는지 확인해 보세요.

```cpp
#include <windows.h>

LRESULT CALLBACK WndProc(HWND hwnd, UINT iMsg, WPARAM wParam, LPARAM lParam)
{
    switch (iMsg)
    {
    case WM_DESTROY:
        PostQuitMessage(0);
        return 0;
    }
    return DefWindowProc(hwnd, iMsg, wParam, lParam);
}

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE, LPSTR, int nCmdShow)
{
    WNDCLASS wc = {};
    wc.lpfnWndProc = WndProc;
    wc.hInstance = hInstance;
    wc.hCursor = LoadCursor(NULL, IDC_ARROW);
    wc.hbrBackground = (HBRUSH)(COLOR_WINDOW + 1);
    wc.lpszClassName = L"LoopClass";
    RegisterClass(&wc);

    HWND hwnd = CreateWindow(
        L"LoopClass", L"메시지 루프",
        WS_OVERLAPPEDWINDOW,
        CW_USEDEFAULT, CW_USEDEFAULT, 640, 480,
        NULL, NULL, hInstance, NULL);

    ShowWindow(hwnd, nCmdShow);
    UpdateWindow(hwnd);

    MSG msg = {};
    while (GetMessage(&msg, NULL, 0, 0))
    {
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }
    return (int)msg.wParam;
}
```

**예상 결과**

```text
창이 유지됨. 닫기 버튼 → 프로그램 종료
```

**코드 해석**

- `WM_DESTROY`에서 `PostQuitMessage(0)`을 호출하면 `WM_QUIT`가 들어가 `GetMessage`가 0을 반환합니다.
- `DispatchMessage`가 `WndProc`을 부릅니다.
- 아직 그리지 않은 메시지는 `DefWindowProc`이 기본 처리를 합니다.

Part 01의 **기본 구조**는 여기까지입니다. 다음 장에서 `WndProc`과 메시지 종류를 늘립니다.

### 연습문제

**문제 1**

- 문제: 메시지 루프 세 함수를 호출 순서대로 쓰세요.
- 입력: 없음
- 출력: 세 이름
- 조건: `05-1`

**문제 2**

- 문제: 창을 닫을 때 루프를 끝내려면 `WM_DESTROY`에서 무엇을 호출하나요?
- 입력: 없음
- 출력: 함수 호출
- 조건: 본문 예제

**문제 3**

- 문제: 위 프로그램을 실행한 뒤 창을 닫아 종료를 확인하세요.
- 입력: 없음
- 출력: 정상 종료
- 조건: 메시지 루프 유지

### 정답 포인트

- 문제 1: GetMessage → TranslateMessage → DispatchMessage
- 문제 2: `PostQuitMessage(0)`
- 문제 3: 닫기 후 프로세스 종료

---

[상위 문서로 돌아가기](./README.md)
