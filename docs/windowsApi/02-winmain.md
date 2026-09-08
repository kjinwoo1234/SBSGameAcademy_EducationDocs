# Chapter 02 WinMain과 진입점

## 학습 목표

- `WinMain`이 창 프로그램의 시작점임을 설명할 수 있다.
- `hInstance`, `nCmdShow`가 무엇인지 한 줄로 말할 수 있다.
- 빈 `WinMain` 뼈대를 Visual Studio Windows 프로젝트에 넣을 수 있다.

## 본문

### 02-1 WinMain이 하는 일

콘솔의 `main`처럼, Windows 창 프로그램은 보통 **`WinMain`**에서 시작합니다. 여기서 창 종류를 등록하고, 창을 만들고, 메시지 루프를 돌립니다. 세부 단계는 뒤 장에서 채웁니다. 이번 장은 **시작 함수의 매개변수**에 집중합니다.

```cpp
int WINAPI WinMain(
    HINSTANCE hInstance,
    HINSTANCE hPrevInstance,
    LPSTR     lpszCmdLine,
    int       nCmdShow
);
```

**`WINAPI`**는 호출 규약 표시입니다. 지금은 “Win32가 정한 형태로 호출된다” 정도로만 두고, 이름을 지우지 마세요.

| 매개변수 | 뜻 |
|----------|-----|
| `hInstance` | 이 프로그램 실행본의 **인스턴스 핸들**. 리소스·창 등록에 자주 넘김 |
| `hPrevInstance` | 옛 16비트 호환용. 현재는 거의 항상 `NULL`이라 **무시해도 됨** |
| `lpszCmdLine` | 명령줄 인자 문자열 |
| `nCmdShow` | 창을 처음 어떤 크기·상태로 보일지 힌트. `ShowWindow`에 넘김 |

**핸들**은 Windows가 내부 대상을 가리킬 때 쓰는 **번호표**에 가깝습니다. `HWND`는 창, `HINSTANCE`는 프로그램 인스턴스입니다. 값을 직접 해석하기보다 “다음에 API에 넘겨 줄 신분증”으로 생각하면 됩니다.

### 02-2 최소 뼈대

Visual Studio에서 **Windows 데스크톱 애플리케이션** 프로젝트를 만든 뒤, 시작 부분을 아래처럼 두면 `WinMain`이 호출되는지 확인할 수 있습니다. 아직 창을 만들지 않았으므로, 메시지 상자만 띄우고 끝냅니다.

아래를 실행해 `WinMain`이 시작되는지 확인해 보세요.

```cpp
#include <windows.h>

int WINAPI WinMain(
    HINSTANCE hInstance,
    HINSTANCE hPrevInstance,
    LPSTR lpszCmdLine,
    int nCmdShow)
{
    MessageBox(NULL, L"WinMain 시작", L"Chapter 02", MB_OK);
    return 0;
}
```

**예상 결과**

```text
제목 "Chapter 02", 내용 "WinMain 시작"인 메시지 상자가 뜬 뒤 확인을 누르면 종료
```

**명령어 해석.** `MessageBox`는 간단한 대화상자를 띄웁니다. `L"..."`는 와이드 문자열 리터럴입니다. 유니코드 설정 프로젝트에서 자주 씁니다. `hPrevInstance`와 `lpszCmdLine`은 이 예제에서 쓰지 않습니다.

이 장 범위는 **`WinMain` 매개변수와 시작 확인**입니다. `WNDCLASS`와 창 생성은 **다음 장**에서 이어집니다.

### 연습문제

**문제 1**

- 문제: `hPrevInstance`를 지금 과정에서 어떻게 다루면 되는지 한 문장으로 쓰세요.
- 입력: 없음
- 출력: 한 문장
- 조건: `02-1` 표

**문제 2**

- 문제: `nCmdShow`를 나중에 넘기게 될 함수 이름을 쓰세요.
- 입력: 없음
- 출력: 함수 이름
- 조건: `02-1`

**문제 3**

- 문제: `MessageBox`만 띄우는 `WinMain`을 실행해 창이 뜨는지 확인하세요.
- 입력: 없음
- 출력: 메시지 상자
- 조건: `WinMain` 시그니처 유지

### 정답 포인트

- 문제 1: 거의 항상 NULL이므로 무시
- 문제 2: `ShowWindow`
- 문제 3: 프로젝트가 Windows 앱인지, 진입점이 `WinMain`인지 확인

---

[상위 문서로 돌아가기](./README.md)
