# Chapter 12 타이머와 InvalidateRect

## 학습 목표

- `SetTimer`로 주기적 `WM_TIMER`를 받을 수 있다.
- 타이머에서 상태를 갱신하고 `InvalidateRect`로 그리는 흐름을 설명할 수 있다.
- 간단한 게임 루프 형태를 코드로 연결할 수 있다.

## 본문

### 12-1 왜 타이머가 필요한가

키·마우스만으로는 “아무 입력 없어도 공이 움직인다”를 만들기 어렵습니다.  
Windows에 **일정 간격으로 깨워 달라**고 요청하는 것이 **`SetTimer`**입니다.

```cpp
SetTimer(hwnd, 1, 16, NULL);  // id 1, 약 16ms ≈ 60fps 감각
```

| 인자 | 뜻 |
|------|-----|
| `hwnd` | 타이머를 받을 창 |
| 타이머 ID | 창 안에서 구분하는 번호 |
| 간격 ms | 몇 밀리초마다 `WM_TIMER`를 보낼지 |
| 콜백 | `NULL`이면 `WM_TIMER`로 수신 |

종료 시에는 `KillTimer(hwnd, 1);`로 정리합니다. `WM_DESTROY`에서 호출하면 안전합니다.

### 12-2 게임 루프에 가까운 흐름

```text
WM_TIMER
   ↓
Update  (좌표·속도 갱신)
   ↓
InvalidateRect()
   ↓
WM_PAINT
   ↓
Render  (그리기)
```

아래는 x 좌표가 계속 증가하는 예입니다.

```cpp
static int g_x = 0;

case WM_CREATE:
    SetTimer(hwnd, 1, 16, NULL);
    return 0;

case WM_TIMER:
    g_x += 2;
    if (g_x > 600) g_x = 0;
    InvalidateRect(hwnd, NULL, TRUE);
    return 0;

case WM_PAINT:
{
    PAINTSTRUCT ps;
    HDC hdc = BeginPaint(hwnd, &ps);
    Ellipse(hdc, g_x, 100, g_x + 40, 140);
    EndPaint(hwnd, &ps);
    return 0;
}

case WM_DESTROY:
    KillTimer(hwnd, 1);
    PostQuitMessage(0);
    return 0;
```

**예상 결과**

```text
원이 오른쪽으로 미끄러지듯 이동하다 왼쪽에서 다시 시작
```

**코드 해석**

- `WM_TIMER`는 **논리 갱신**만 하고, 그리기는 `WM_PAINT`에 둡니다.
- `InvalidateRect`의 세 번째 인자가 `TRUE`면 배경을 지우고 다시 그립니다. 깜빡임이 커지면 더블 버퍼링 등 심화 기법이 필요하지만, 입문 게임에는 이 흐름으로 충분합니다.

다음 장에서 입력·충돌·페인트를 모아 **공 튕기기**를 만듭니다.

### 연습문제

**문제 1**

- 문제: 16ms 간격 타이머를 거는 `SetTimer` 호출을 쓰세요. ID는 1.
- 입력: 없음
- 출력: 한 줄 코드
- 조건: `12-1`

**문제 2**

- 문제: Update 다음 화면을 갱신하려면 어떤 함수를 호출하나요?
- 입력: 없음
- 출력: 함수 이름
- 조건: `12-2`

**문제 3**

- 문제: 원이 스스로 움직이는 프로그램을 실행해 확인하세요.
- 입력: 없음
- 출력: 자동 이동
- 조건: `KillTimer`를 `WM_DESTROY`에 둘 것

### 정답 포인트

- 문제 1: `SetTimer(hwnd, 1, 16, NULL);`
- 문제 2: `InvalidateRect`
- 문제 3: CREATE에서 SetTimer, TIMER에서 좌표, PAINT에서 그림

---

[상위 문서로 돌아가기](./README.md)
