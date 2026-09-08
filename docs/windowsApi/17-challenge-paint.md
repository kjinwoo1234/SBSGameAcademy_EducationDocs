# Chapter 17 도전! Win32 그림판

## 학습 목표

- 마우스 다운·무브·업으로 자유 곡선을 그릴 수 있다.
- 그리기 상태를 보관하고 `WM_PAINT`에서 다시 그릴 수 있다.
- GDI와 마우스 메시지를 한 프로그램에 연결할 수 있다.

## 본문

### 17-1 목표

간단한 그림판입니다. 왼쪽 버튼을 누른 채 움직이면 선이 남습니다.

```text
배우는 것:
마우스 · WM_MOUSEMOVE · WM_LBUTTONDOWN · GDI · HDC
```

### 17-2 설계 힌트

1. `bool drawing` — 버튼을 누른 상태인지
2. `int lastX, lastY` — 직전 점
3. 선분 목록 — `vector`에 `{x1,y1,x2,y2}`를 쌓거나, 메모리 비트맵에 누적

입문에서는 **선분 목록**이 이해하기 쉽습니다.

```cpp
case WM_LBUTTONDOWN:
    drawing = true;
    lastX = LOWORD(lParam);
    lastY = HIWORD(lParam);
    return 0;

case WM_MOUSEMOVE:
    if (drawing)
    {
        int x = LOWORD(lParam);
        int y = HIWORD(lParam);
        // 선분 (lastX,lastY)-(x,y) 저장
        lastX = x;
        lastY = y;
        InvalidateRect(hwnd, NULL, FALSE);
    }
    return 0;

case WM_LBUTTONUP:
    drawing = false;
    return 0;
```

`WM_PAINT`에서 저장한 선분을 `MoveToEx` / `LineTo`로 모두 다시 그립니다.  
`InvalidateRect`의 지우기 플래그를 `FALSE`로 두면 깜빡임이 줄어드는 경우가 있습니다.

메뉴에 “지우기”를 두면 `WM_COMMAND`에서 선분 목록을 비우면 됩니다.

### 연습문제

**문제 1**

- 문제: 드래그로 선을 그리는 그림판을 만드세요.
- 입력: 마우스 드래그
- 출력: 창에 선
- 조건: down/move/up 사용

**문제 2**

- 문제: 창을 가렸다가 다시 보여도 그림이 남게 하세요.
- 입력: 다른 창으로 가림
- 출력: 선 유지
- 조건: `WM_PAINT`에서 재구성

**문제 3**

- 문제: 메뉴 또는 버튼으로 화면을 지우는 기능을 추가하세요.
- 입력: 지우기 명령
- 출력: 빈 캔버스
- 조건: `WM_COMMAND`

### 정답 포인트

- 문제 1: drawing 플래그 + 선분 저장
- 문제 2: 페인트에서 목록 전체 재그리기
- 문제 3: 목록 clear + InvalidateRect

---

[상위 문서로 돌아가기](./README.md)
