# Chapter 11 도전! Win32 계산기

## 학습 목표

- EDIT·BUTTON·`WM_COMMAND`로 간단한 계산기 UI를 구성할 수 있다.
- 버튼 ID마다 입력 문자열을 이어 붙이거나 계산할 수 있다.
- Part 05까지 배운 컨트롤·메시지를 한 프로그램에 연결할 수 있다.

## 본문

### 11-1 목표 화면

두 숫자와 연산이 아니라, 입문용으로 **표시창 하나 + 숫자·연산 버튼**이면 충분합니다.

```text
┌────────────────────┐
│  [  표시 EDIT  ]   │
│  7  8  9  /        │
│  4  5  6  *        │
│  1  2  3  -        │
│  0  C  =  +        │
└────────────────────┘
```

### 11-2 설계 힌트

1. `WM_CREATE`에서 EDIT와 BUTTON들을 `CreateWindow`로 만듭니다.
2. 숫자 버튼 ID를 `1000 + 숫자`처럼 규칙적으로 둡니다.
3. `WM_COMMAND`에서 ID를 보고 EDIT 텍스트 뒤에 글자를 붙입니다.
4. `=`에서는 문자열을 파싱해 계산합니다. 입문은 `a + b` 한 번만 지원해도 됩니다.
5. `C`는 EDIT를 비웁니다.

문자열을 숫자로 바꿀 때는 `_wtoi` 또는 직접 파싱을 씁니다. 복잡한 수식 엔진은 이 장 범위가 아닙니다.

### 11-3 최소 동작 예

숫자와 `+`, `=`만 있는 골격입니다. 버튼을 더 붙여 완성해 보세요.

```cpp
// WM_CREATE에서
CreateWindow(L"EDIT", L"0",
    WS_CHILD | WS_VISIBLE | WS_BORDER | ES_RIGHT,
    10, 10, 200, 28, hwnd, (HMENU)1, hInst, NULL);
CreateWindow(L"BUTTON", L"1", WS_CHILD | WS_VISIBLE,
    10, 50, 40, 40, hwnd, (HMENU)1001, hInst, NULL);
CreateWindow(L"BUTTON", L"+", WS_CHILD | WS_VISIBLE,
    60, 50, 40, 40, hwnd, (HMENU)2001, hInst, NULL);
CreateWindow(L"BUTTON", L"=", WS_CHILD | WS_VISIBLE,
    110, 50, 40, 40, hwnd, (HMENU)2002, hInst, NULL);
```

**이 장에서 배울 것**

```text
윈도우 · 버튼 · EDIT · WM_COMMAND
```

### 연습문제

**문제 1**

- 문제: 숫자 0~9 버튼과 `+`, `-`, `=`, `C`를 배치하세요.
- 입력: 없음
- 출력: 계산기 형태의 창
- 조건: 모든 버튼에 서로 다른 ID

**문제 2**

- 문제: `12+3=` 순서로 누르면 결과에 `15`가 보이게 하세요.
- 입력: 버튼 클릭
- 출력: EDIT에 `15`
- 조건: `WM_COMMAND`만으로 흐름 완성

**문제 3**

- 문제: 잘못된 식에서 프로그램이 죽지 않게, 실패 시 `"오류"`를 EDIT에 넣으세요.
- 입력: 불완전한 식
- 출력: `오류` 또는 무시
- 조건: 예외로 크래시 내지 않기

### 정답 포인트

- 문제 1: `WM_CREATE`에서 컨트롤 생성
- 문제 2: 피연산자·연산자 상태 변수 후 `=`에서 계산
- 문제 3: 파싱 실패 분기

---

[상위 문서로 돌아가기](./README.md)
