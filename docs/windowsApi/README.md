# Win32 API 자습 자료

Windows 프로그램이 **창을 만들고, 메시지를 받고, 그리고, 입력에 반응하는** 원리를 익히는 과정입니다.  
목표는 Win32 API를 실무 수준으로 전부 외우는 것이 아니라, **게임·엔진으로 넘어가기 전에 Windows 동작 구조를 이해하는 것**입니다.

## 권장 대상

- C++로 콘솔 프로그램을 만들 수 있는 학습자
- 나중에 DirectX·엔진을 볼 때 “창과 메시지”가 어디서 오는지 알고 싶은 학습자

## 선수지식

- C++ 기본 문법: 함수, 조건문, 반복문, 구조체
- Visual Studio에서 C++ 프로젝트를 만들고 실행하는 방법

## 학습 방향

```text
윈도우 생성 → 메시지 → 그리기(GDI) → 입력 → 리소스·컨트롤 → 타이머 → 작은 게임 → DirectX
```

API를 하나씩만 나열하기보다, **작은 프로그램을 만들며** 위 흐름을 연결하는 것을 권장합니다.

## 목차

### Part 01. Win32 기본 구조

- [Chapter 01 윈도우 프로그램이 움직이는 방식](./01-windows-program-flow.md)
- [Chapter 02 WinMain과 진입점](./02-winmain.md)
- [Chapter 03 WNDCLASS와 RegisterClass](./03-wndclass-register.md)
- [Chapter 04 CreateWindow와 창 표시](./04-create-show-window.md)
- [Chapter 05 메시지 루프](./05-message-loop.md)

### Part 02. WndProc와 메시지

- [Chapter 06 WndProc와 기본 메시지](./06-wndproc-messages.md)

### Part 03. 화면에 그리기

- [Chapter 07 WM_PAINT와 GDI](./07-paint-gdi.md)

### Part 04. 키보드·마우스 입력

- [Chapter 08 키보드 입력](./08-keyboard-input.md)
- [Chapter 09 마우스 입력](./09-mouse-input.md)

### Part 05. 메뉴·컨트롤

- [Chapter 10 메뉴·버튼·EDIT](./10-menu-controls.md)
- [Chapter 11 도전! Win32 계산기](./11-challenge-calculator.md)

### Part 06. 타이머와 게임 루프

- [Chapter 12 타이머와 InvalidateRect](./12-timer.md)
- [Chapter 13 도전! 공 튕기기](./13-challenge-bounce.md)

### Part 07. 데이터·리소스·다음 단계

- [Chapter 14 윈도우와 데이터 연결](./14-window-userdata.md)
- [Chapter 15 파일·리소스·다이얼로그](./15-file-resource.md)
- [Chapter 16 DirectX로 넘어가기](./16-toward-directx.md)

### Part 08. 추가 미니 프로젝트

- [Chapter 17 도전! Win32 그림판](./17-challenge-paint.md)
- [Chapter 18 도전! 벽돌깨기](./18-challenge-breakout.md)
