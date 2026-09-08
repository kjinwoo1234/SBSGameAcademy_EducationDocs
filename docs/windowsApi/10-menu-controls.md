# Chapter 10 메뉴·버튼·EDIT

## 학습 목표

- 메뉴를 만들고 `WM_COMMAND`로 선택 항목을 받을 수 있다.
- `CreateWindow`로 `BUTTON` / `EDIT` / `STATIC` 자식 컨트롤을 만들 수 있다.
- 버튼 클릭을 `WM_COMMAND`의 `LOWORD(wParam)`으로 구분할 수 있다.

## 본문

### 10-1 메뉴와 WM_COMMAND

`CreateWindow`의 `hMenu`에 메뉴 핸들을 넘기면 제목 줄 아래 메뉴가 붙습니다.  
메뉴 항목을 고르면 **`WM_COMMAND`**가 옵니다. 어떤 항목인지는 `LOWORD(wParam)`의 **명령 ID**로 구분합니다.

```cpp
HMENU hMenu = CreateMenu();
HMENU hFile = CreatePopupMenu();
AppendMenu(hFile, MF_STRING, 1001, L"종료");
AppendMenu(hMenu, MF_POPUP, (UINT_PTR)hFile, L"파일");

// CreateWindow(..., hMenu, hInstance, NULL);

case WM_COMMAND:
    if (LOWORD(wParam) == 1001)
    {
        DestroyWindow(hwnd);
    }
    return 0;
```

| API | 역할 |
|-----|------|
| `CreateMenu` | 최상위 메뉴 막대 |
| `CreatePopupMenu` | 드롭다운 메뉴 |
| `AppendMenu` | 항목 추가 |

### 10-2 버튼·EDIT·STATIC

자식 창으로 표준 컨트롤을 만들 수 있습니다. 클래스 이름 문자열로 종류를 고릅니다.

| 클래스 | 용도 |
|--------|------|
| `"BUTTON"` | 버튼 |
| `"EDIT"` | 한 줄·여러 줄 입력 |
| `"STATIC"` | 라벨 글자 |

```cpp
CreateWindow(L"STATIC", L"이름:",
    WS_CHILD | WS_VISIBLE,
    20, 20, 60, 24, hwnd, NULL, hInstance, NULL);

CreateWindow(L"EDIT", L"",
    WS_CHILD | WS_VISIBLE | WS_BORDER,
    90, 20, 160, 24, hwnd, (HMENU)2001, hInstance, NULL);

CreateWindow(L"BUTTON", L"확인",
    WS_CHILD | WS_VISIBLE,
    90, 60, 80, 28, hwnd, (HMENU)2002, hInstance, NULL);
```

대략 배치는 다음과 같습니다.

```text
┌───────────────────────────────┐
│ 파일                          │
├───────────────────────────────┤
│ 이름: [______________]        │
│          [ 확인 ]             │
└───────────────────────────────┘
```

버튼은 `WM_COMMAND`에서 `LOWORD(wParam) == 2002`로 받습니다. EDIT 내용은 `GetWindowText`로 읽습니다.

```cpp
case WM_COMMAND:
    if (LOWORD(wParam) == 2002)
    {
        wchar_t name[128];
        GetDlgItemText(hwnd, 2001, name, 128);
        MessageBox(hwnd, name, L"입력값", MB_OK);
    }
    return 0;
```

**코드 해석**

- 자식 컨트롤의 `hMenu` 자리에 넣은 값은 **컨트롤 ID**로 쓰입니다.
- `GetDlgItemText`는 부모 창과 ID로 텍스트를 읽습니다.
- 메뉴·버튼이 같은 `WM_COMMAND`를 쓰므로 ID를 겹치지 않게 정합니다.

전체 UI를 이어서 만드는 연습은 **다음 장 계산기 도전**에서 합니다.

### 연습문제

**문제 1**

- 문제: 메뉴 항목·버튼 클릭을 받는 메시지 이름을 쓰세요.
- 입력: 없음
- 출력: 메시지 이름
- 조건: `10-1`

**문제 2**

- 문제: 한 줄 입력 칸에 쓰는 컨트롤 클래스 이름을 쓰세요.
- 입력: 없음
- 출력: 클래스 문자열
- 조건: `10-2` 표

**문제 3**

- 문제: STATIC + EDIT + BUTTON을 하나 만들고, 확인 시 메시지 상자로 EDIT 내용을 보여 주세요.
- 입력: 문자열 입력 후 확인
- 출력: 메시지 상자
- 조건: `WM_COMMAND`로 구분

### 정답 포인트

- 문제 1: `WM_COMMAND`
- 문제 2: `"EDIT"` / `L"EDIT"`
- 문제 3: ID + `GetDlgItemText` 또는 `GetWindowText`

---

[상위 문서로 돌아가기](./README.md)
