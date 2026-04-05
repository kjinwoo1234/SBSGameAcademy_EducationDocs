# Chapter 26 콘솔 제어로 뱀/테트리스 만들기

## 학습 목표
- C++에서 콘솔 커서 이동/색상/화면 제어를 구현할 수 있다.
- 클래스 기반으로 뱀게임/테트리스 루프를 구조화할 수 있다.

## 세부 주제
### 26-1 콘솔 제어 유틸
- 좌표 이동, 색상, 화면 지우기

### 26-2 뱀게임 아키텍처
- `Snake`, `Food`, `Game` 클래스 분리

### 26-3 테트리스 아키텍처
- 보드/블록/회전/줄 삭제 분리

## 실습 체크리스트
- 콘솔 유틸 클래스와 게임 루프 클래스를 분리해 구현한다.

## 본문

### 26-1 콘솔 제어 유틸
- 콘솔 게임은 좌표 출력 기반 렌더링이 핵심입니다.
- 커서 이동/색상/클리어 기능을 `ConsoleUtil`로 묶어두면 두 게임에서 공통 사용이 가능합니다.

함수 비교(Windows 기준):
| 기능 | API | 사용 목적 |
|---|---|---|
| 커서 이동 | `SetConsoleCursorPosition` | 특정 좌표 출력 |
| 색상 설정 | `SetConsoleTextAttribute` | 블록/점수/경고 강조 |
| 화면 지우기 | `system("cls")` 또는 버퍼 출력 | 프레임 갱신 |

용어 설명:
| 용어 | 의미 |
|---|---|
| 렌더링(rendering) | 현재 게임 상태를 화면에 그려 보여주는 과정 |
| 프레임(frame) | 화면이 한 번 갱신된 결과 |

예시 코드:
```cpp
#include <windows.h>
#include <iostream>

class ConsoleUtil {
public:
    static void MoveTo(int x, int y) {
        COORD p = {(SHORT)x, (SHORT)y};
        SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), p);
    }
    static void SetColor(WORD color) {
        SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
    }
    static void Clear() {
        system("cls");
    }
};
```

예상 결과:
```text
좌표 기반 출력과 색상 변경이 동작
```

코드 해석:
- 정적 함수로 구성해 어디서나 간단히 호출할 수 있게 했습니다.
- 유틸과 게임 로직을 분리하면 테스트/수정 범위가 줄어듭니다.

### 26-2 뱀게임 아키텍처
- `Snake`는 몸통 좌표와 이동/성장 로직을 담당합니다.
- `Food`는 생성 위치 관리, `Game`은 입력/루프/충돌 판정을 담당합니다.

### 26-3 테트리스 아키텍처
- 보드(`Board`)와 블록(`Tetromino`)을 분리하면 충돌 검사와 줄 삭제가 단순해집니다.
- 회전 로직은 블록 좌표 변환 함수로 독립시키는 것이 좋습니다.

연습문제:
1. 문제 1 - `ConsoleUtil` 클래스 작성
   - 문제: `MoveTo`, `SetColor`, `Clear`를 갖는 유틸 클래스를 구현하세요.
   - 입력: 없음
   - 출력: 데모 화면
   - 조건: static 함수로 작성
2. 문제 2 - 뱀게임 최소 루프
   - 문제: 뱀 머리 이동/충돌 종료를 구현하세요.
   - 입력: 방향키
   - 출력: 좌표 이동
   - 조건: 틱 루프와 입력 처리 분리
3. 문제 3 - 테트리스 줄 삭제
   - 문제: 꽉 찬 줄을 감지해 삭제하고 위 줄을 내리는 함수를 작성하세요.
   - 입력: 보드 배열
   - 출력: 갱신된 보드
   - 힌트: 아래에서 위로 검사

정답 포인트:
- 콘솔 제어와 게임 로직 분리
- 뱀/테트리스 공통 패턴은 게임 루프 구조
- 테트리스는 보드 상태 관리가 핵심 난이도 포인트

---

[상위 문서로 돌아가기](./README.md)
