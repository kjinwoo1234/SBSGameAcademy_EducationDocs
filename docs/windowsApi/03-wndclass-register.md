# Chapter 03 WNDCLASS와 RegisterClass

## 학습 목표

- `WNDCLASS`가 창 종류의 설계도임을 설명할 수 있다.
- `lpfnWndProc`, `hInstance`, `lpszClassName`의 역할을 구분할 수 있다.
- `RegisterClass`로 클래스를 등록하는 코드를 작성할 수 있다.

## 본문

### 03-1 WNDCLASS — 창의 설계도

창을 만들기 전에 “이 종류의 창은 **어느 함수가 메시지를 받고**, **배경색은 무엇이며**, **이름은 무엇인가**”를 적어 둡니다. 그 묶음이 **`WNDCLASS`**입니다.

자주 채우는 멤버만 먼저 봅니다.

| 멤버 | 역할 |
|------|------|
| `lpfnWndProc` | 메시지를 처리할 함수 주소. 보통 `WndProc` |
| `hInstance` | `WinMain`에서 받은 인스턴스 |
| `hCursor` | 창 위 마우스 커서 |
| `hbrBackground` | 배경 브러시 |
| `lpszClassName` | 이 설계도의 이름 문자열 |
| `style` | 클래스 스타일. 입문에선 `CS_HREDRAW \| CS_VREDRAW` 정도 |

`WndProc` 본문은 **메시지 장**에서 자세히 씁니다. 지금은 **등록에 필요한 주소**만 연결합니다.

### 03-2 RegisterClass

`RegisterClass`는 설계도를 Windows에 **등록**합니다. 성공하면 그 이름(`lpszClassName`)으로 나중에 `CreateWindow`를 호출할 수 있습니다.

아래는 등록까지만 하는 골격입니다. `WndProc`는 아직 `DefWindowProc`에 맡깁니다.

```cpp
#include <windows.h>

LRESULT CALLBACK WndProc(HWND hwnd, UINT iMsg, WPARAM wParam, LPARAM lParam)
{
    return DefWindowProc(hwnd, iMsg, wParam, lParam);
}

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE, LPSTR, int)
{
    WNDCLASS wc = {};
    wc.lpfnWndProc = WndProc;
    wc.hInstance = hInstance;
    wc.hCursor = LoadCursor(NULL, IDC_ARROW);
    wc.hbrBackground = (HBRUSH)(COLOR_WINDOW + 1);
    wc.lpszClassName = L"MyFirstClass";

    if (!RegisterClass(&wc))
    {
        MessageBox(NULL, L"RegisterClass 실패", L"오류", MB_OK);
        return 0;
    }

    MessageBox(NULL, L"등록 성공", L"Chapter 03", MB_OK);
    return 0;
}
```

**예상 결과**

```text
"등록 성공" 메시지 상자
```

**코드 해석**

- `WNDCLASS wc = {};`로 나머지를 0에 가깝게 비웁니다.
- `lpfnWndProc`에 `WndProc`를 연결합니다.
- `RegisterClass(&wc)`가 0이면 실패입니다.

창을 화면에 띄우는 단계는 **다음 장** `CreateWindow`에서 이어집니다.

### 연습문제

**문제 1**

- 문제: 메시지 처리 함수를 가리키는 `WNDCLASS` 멤버 이름을 쓰세요.
- 입력: 없음
- 출력: 멤버 이름
- 조건: `03-1` 표

**문제 2**

- 문제: `CreateWindow`가 찾을 설계도 이름은 어느 멤버에 넣나요?
- 입력: 없음
- 출력: 멤버 이름
- 조건: `03-1`

**문제 3**

- 문제: `RegisterClass` 실패 시 메시지 상자를 띄우도록 위 예제를 실행·확인하세요.
- 입력: 없음
- 출력: 성공 또는 실패 상자
- 조건: `lpszClassName`을 비우지 말 것

### 정답 포인트

- 문제 1: `lpfnWndProc`
- 문제 2: `lpszClassName`
- 문제 3: 등록 성공 시 다음 장에서 창 생성 가능

---

[상위 문서로 돌아가기](./README.md)
