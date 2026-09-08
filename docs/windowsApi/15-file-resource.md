# Chapter 15 파일·리소스·다이얼로그

## 학습 목표

- 리소스에 아이콘·비트맵·대화상자를 넣을 수 있다는 것을 설명할 수 있다.
- `LoadBitmap` / `LoadIcon` / `LoadCursor`의 역할을 구분할 수 있다.
- 간단한 파일 저장·열기 흐름을 Win32 관점에서 말할 수 있다.

## 본문

### 15-1 리소스란

**리소스**는 실행 파일에 붙이는 아이콘·커서·비트맵·메뉴·대화상자 같은 **데이터**입니다. 코드와 분리해 두면 그림·UI를 바꾸기 쉽습니다. Visual Studio에서는 `.rc` 리소스 스크립트와 리소스 뷰로 관리하는 경우가 많습니다.

| 종류 | 대략적인 용도 |
|------|----------------|
| Icon | 창·실행 파일 아이콘 |
| Cursor | 마우스 모양 |
| Bitmap | 이미지 |
| Dialog | 미리 배치한 대화상자 |
| Menu | 메뉴를 리소스로 정의 |

코드에서 불러올 때는 예를 들어 다음과 같습니다.

```cpp
HICON hIcon = LoadIcon(hInstance, MAKEINTRESOURCE(IDI_APP));
HCURSOR hCur = LoadCursor(hInstance, MAKEINTRESOURCE(IDC_MYCUR));
HBITMAP hBmp = LoadBitmap(hInstance, MAKEINTRESOURCE(IDB_BALL));
```

`MAKEINTRESOURCE`는 리소스 ID 숫자를 이름 자리에 넘기기 위한 매크로입니다. ID 이름은 리소스 헤더에 정의됩니다.

### 15-2 비트맵을 창에 그리기

비트맵을 그릴 때는 메모리 DC에 비트맵을 고른 뒤 `BitBlt`로 화면 DC에 복사하는 패턴이 흔합니다.

```cpp
HDC hdcMem = CreateCompatibleDC(hdc);
HBITMAP old = (HBITMAP)SelectObject(hdcMem, hBmp);
BitBlt(hdc, 0, 0, width, height, hdcMem, 0, 0, SRCCOPY);
SelectObject(hdcMem, old);
DeleteDC(hdcMem);
```

입문에서는 **로드 → BitBlt → 정리** 순서만 기억해도 됩니다. 스프라이트 시트·알파 블렌딩은 DirectX·엔진 단계에서 더 자주 다룹니다.

### 15-3 파일과 공통 대화상자

텍스트나 설정을 디스크에 남기려면 C++ 파일 입출력(`fstream`)도 쓸 수 있고, Windows **공통 대화상자**로 경로를 고를 수도 있습니다.

- `GetOpenFileName` — 열기 대화상자
- `GetSaveFileName` — 저장 대화상자

대화상자에서 경로를 받은 뒤, 내용을 읽거나 쓰면 됩니다. 세부 플래그는 MSDN식 문서보다 **한 번 열기·저장 예제**를 따라 하며 익히는 편이 빠릅니다.

다이얼로그 리소스(`DialogBox`)는 컨트롤을 미리 배치한 창을 띄울 때 씁니다. 계산기·설정 창을 리소스로 옮기는 연습에 좋습니다.

이 장 범위는 **리소스·파일·다이얼로그가 어디에 쓰이는지**입니다. 엔진처럼 자산 파이프라인을 만드는 단계는 다음 장 이후의 영역입니다.

### 연습문제

**문제 1**

- 문제: 실행 파일에 붙이는 아이콘·비트맵 묶음을 부르는 말을 쓰세요.
- 입력: 없음
- 출력: 용어
- 조건: `15-1`

**문제 2**

- 문제: 비트맵을 화면 DC로 복사할 때 자주 쓰는 함수 이름을 쓰세요.
- 입력: 없음
- 출력: 함수 이름
- 조건: `15-2`

**문제 3**

- 문제: 리소스로 아이콘을 하나 추가하고, `WNDCLASS`의 `hIcon`에 넣어 창 아이콘을 바꿔 보세요.
- 입력: 없음
- 출력: 제목 줄·Alt-Tab 등에 아이콘 반영
- 조건: `LoadIcon` 사용

### 정답 포인트

- 문제 1: 리소스
- 문제 2: `BitBlt`
- 문제 3: `wc.hIcon = LoadIcon(...)` 후 RegisterClass

---

[상위 문서로 돌아가기](./README.md)
