# Chapter 07 WM_PAINT와 GDI

## 학습 목표

- `WM_PAINT`에서 `BeginPaint` / `EndPaint`로 그리는 흐름을 설명할 수 있다.
- `HDC`가 그리기 통로임을 한 줄로 말할 수 있다.
- `Rectangle`, `Ellipse`, `TextOut`으로 간단한 도형·글자를 그릴 수 있다.

## 본문

### 07-1 GDI와 HDC

창 안에 도형·글자를 그리려면 Windows의 **GDI**를 씁니다. GDI는 Graphics Device Interface, 화면에 그리는 기본 인터페이스입니다.

**`HDC`**는 Device Context 핸들입니다. “어디에, 어떤 펜·브러시로 그릴지”를 담은 **그리기 통로**라고 보면 됩니다. `WM_PAINT`에서는 보통 아래 순서를 지킵니다.

```text
BeginPaint()  →  HDC 얻기
도형·글자 그리기
EndPaint()    →  그리기 끝, 무효 영역 정리
```

`BeginPaint` / `EndPaint` 쌍을 빼먹으면 창이 계속 다시 그리라고 요청하거나, 깜빡임·무한 페인트가 날 수 있습니다.

### 07-2 WM_PAINT에서 도형 그리기

아래를 `WndProc`의 `WM_PAINT`에 넣고 실행해 보세요.

```cpp
case WM_PAINT:
{
    PAINTSTRUCT ps;
    HDC hdc = BeginPaint(hwnd, &ps);

    Rectangle(hdc, 100, 100, 300, 200);
    Ellipse(hdc, 350, 100, 450, 200);
    TextOut(hdc, 100, 220, L"GDI 연습", 6);

    EndPaint(hwnd, &ps);
    return 0;
}
```

**예상 결과**

```text
창 안에 직사각형, 원, 글자 "GDI 연습"
```

대략 배치는 이렇게 상상하면 됩니다.

```text
┌──────────────────────────────┐
│                              │
│    ┌──────────────┐   ○      │
│    │              │          │
│    │              │          │
│    └──────────────┘          │
│    GDI 연습                  │
└──────────────────────────────┘
```

**코드 해석**

- `BeginPaint`가 `HDC`와 `PAINTSTRUCT`를 채웁니다.
- `Rectangle`은 왼쪽·위·오른쪽·아래 좌표로 네모를 그립니다.
- `Ellipse`는 외접 사각형 좌표로 타원·원을 그립니다.
- `TextOut`은 글자를 찍습니다. 마지막 인자는 문자 개수입니다.
- `EndPaint`로 마무리합니다.

선만 그리고 싶으면 `MoveToEx`로 시작점을 두고 `LineTo`로 이으면 됩니다. 색·펜을 바꾸려면 `CreatePen` / `SelectObject` 등이 필요하지만, 입문에서는 **기본 펜으로 도형부터** 익히면 됩니다.

### 07-3 다시 그리기 요청

상태가 바뀌어 화면을 갱신해야 하면 **`InvalidateRect(hwnd, NULL, TRUE)`**를 호출합니다. Windows가 `WM_PAINT`를 보내게 됩니다. 타이머·입력과 연결하는 법은 **타이머 장**에서 이어집니다.

이 장 범위는 **페인트 한 번으로 도형·글자 출력**입니다. 매 프레임 갱신은 아직 아닙니다.

### 연습문제

**문제 1**

- 문제: `WM_PAINT`에서 HDC를 얻는 함수와 끝나는 함수를 쓰세요.
- 입력: 없음
- 출력: 두 함수 이름
- 조건: `07-1`

**문제 2**

- 문제: 네모를 그리는 GDI 함수 이름을 쓰세요.
- 입력: 없음
- 출력: 함수 이름
- 조건: `07-2`

**문제 3**

- 문제: 원 하나와 글자 한 줄을 그리는 `WM_PAINT`를 실행해 확인하세요.
- 입력: 없음
- 출력: 창에 도형·글자
- 조건: `BeginPaint`/`EndPaint` 쌍 유지

### 정답 포인트

- 문제 1: `BeginPaint`, `EndPaint`
- 문제 2: `Rectangle`
- 문제 3: `Ellipse` + `TextOut` 등

---

[상위 문서로 돌아가기](./README.md)
