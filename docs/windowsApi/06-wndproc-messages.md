# Chapter 06 WndProc와 기본 메시지

## 학습 목표

- `WndProc`의 네 매개변수 역할을 설명할 수 있다.
- `WM_CREATE`, `WM_DESTROY`, `WM_PAINT` 등 기본 메시지를 `switch`로 나눌 수 있다.
- 처리하지 않은 메시지는 `DefWindowProc`에 넘기는 이유를 말할 수 있다.

## 본문

### 06-1 WndProc 시그니처

**`WndProc`**은 창에 도착한 메시지를 처리하는 함수입니다. `DispatchMessage`가 호출합니다.

```cpp
LRESULT CALLBACK WndProc(
    HWND hwnd,
    UINT iMsg,
    WPARAM wParam,
    LPARAM lParam);
```

| 매개변수 | 역할 |
|----------|------|
| `hwnd` | 메시지를 받은 창 |
| `iMsg` | 메시지 번호. `WM_PAINT` 등 |
| `wParam` | 메시지별 추가 정보 1 |
| `lParam` | 메시지별 추가 정보 2 |

같은 `wParam`이라도 메시지마다 뜻이 다릅니다. 키 메시지에서는 가상 키 코드, 마우스 메시지에서는 버튼 플래그처럼 **메시지와 짝**으로 읽습니다.

### 06-2 먼저 익힐 메시지

입문에서 자주 만나는 메시지입니다.

| 메시지 | 대략적인 시점 |
|--------|----------------|
| `WM_CREATE` | 창이 막 만들어졌을 때 |
| `WM_DESTROY` | 창이 파괴될 때 |
| `WM_PAINT` | 다시 그려야 할 때 |
| `WM_KEYDOWN` / `WM_KEYUP` | 키를 누르거나 뗄 때 |
| `WM_CHAR` | 문자 입력 |
| `WM_MOUSEMOVE` | 마우스 이동 |
| `WM_LBUTTONDOWN` / `WM_LBUTTONUP` | 왼쪽 버튼 |
| `WM_COMMAND` | 메뉴·버튼 등 명령 |

아래처럼 `switch`로 나눕니다. 그리지 않는 메시지는 `break`만 두고, 나머지는 `DefWindowProc`에 맡깁니다.

```cpp
LRESULT CALLBACK WndProc(HWND hwnd, UINT iMsg, WPARAM wParam, LPARAM lParam)
{
    switch (iMsg)
    {
    case WM_CREATE:
        // 초기화
        return 0;

    case WM_KEYDOWN:
        // 키 입력 — 뒤 장
        return 0;

    case WM_LBUTTONDOWN:
        // 마우스 — 뒤 장
        return 0;

    case WM_PAINT:
        // 그리기 — 다음 장
        break;

    case WM_DESTROY:
        PostQuitMessage(0);
        return 0;
    }

    return DefWindowProc(hwnd, iMsg, wParam, lParam);
}
```

**코드 해석**

- 처리한 메시지는 보통 `0`을 반환합니다.
- `DefWindowProc`은 시스템 기본 동작입니다. 닫기·크기 조절 등을 빼먹으면 창이 이상해집니다.
- `WM_PAINT`에서 실제로 그리는 법은 **다음 장**입니다.

### 06-3 처리 확인용 예제

키를 누르면 메시지 상자로 `wParam`을 보여 줍니다. “메시지가 WndProc까지 온다”는 감각용입니다.

```cpp
case WM_KEYDOWN:
{
    wchar_t buf[64];
    wsprintf(buf, L"WM_KEYDOWN wParam=%u", (unsigned)wParam);
    MessageBox(hwnd, buf, L"메시지", MB_OK);
    return 0;
}
```

**예상 결과**

```text
키를 누를 때마다 숫자(가상 키 코드)가 적힌 상자
```

게임처럼 매 프레임 반응하려면 메시지 상자 대신 상태 변수와 타이머를 씁니다. 입력 세부는 **키보드·마우스 장**, 반복 갱신은 **타이머 장**에서 다룹니다.

### 연습문제

**문제 1**

- 문제: 메시지 종류를 담는 매개변수 이름을 쓰세요.
- 입력: 없음
- 출력: 매개변수 이름
- 조건: `06-1`

**문제 2**

- 문제: 처리하지 않은 메시지를 넘기는 함수 이름을 쓰세요.
- 입력: 없음
- 출력: 함수 이름
- 조건: 본문

**문제 3**

- 문제: `WM_DESTROY`에서 `PostQuitMessage`를 호출하는 `WndProc`을 포함한 프로그램을 실행해 창을 닫아 보세요.
- 입력: 없음
- 출력: 정상 종료
- 조건: `DefWindowProc` 유지

### 정답 포인트

- 문제 1: `iMsg`
- 문제 2: `DefWindowProc`
- 문제 3: 닫기 → 루프 종료

---

[상위 문서로 돌아가기](./README.md)
