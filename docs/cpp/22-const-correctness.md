# Chapter 22 const 정확성과 API 설계

## 학습 목표
- `const`의 의미를 변수/포인터/멤버 함수 관점에서 구분한다.
- API 설계에서 `const`를 활용해 의도를 명확히 표현할 수 있다.

## 세부 주제
### 22-1 `const` 기본
- 값 불변성

### 22-2 포인터/참조와 `const`
- `const int*`, `int* const`, `const int&`

### 22-3 `const` 멤버 함수
- 읽기 전용 인터페이스 설계

## 실습 체크리스트
- 읽기 전용 함수와 수정 함수의 인터페이스를 분리한 클래스를 작성한다.

## 본문

### 22-1 `const` 기본
- `const`는 "이 값은 수정하지 않겠다"는 약속입니다.
- 코드 안정성과 가독성을 함께 높일 수 있습니다.

### 22-2 포인터/참조와 `const`
- `const int* p`: 가리키는 값 수정 금지, 포인터 이동 가능
- `int* const p`: 포인터 이동 금지, 가리키는 값 수정 가능
- `const int& r`: 원본 복사 없이 읽기 전용 참조

### 22-3 `const` 멤버 함수
- 멤버 함수 뒤 `const`는 객체 상태를 바꾸지 않겠다는 의미입니다.
- 읽기 전용 객체(`const 객체`)에서도 호출 가능해 API 활용 범위가 넓어집니다.

약어 설명(API):
- **API**는 **Application Programming Interface**의 약자입니다.
- 한국어로는 "응용 프로그램 프로그래밍 인터페이스"라고 하며, 외부에서 호출할 수 있도록 공개한 함수/클래스 사용 규칙을 뜻합니다.
- 이 챕터에서의 API는 "수정 함수와 조회 함수의 경계를 명확히 공개하는 설계"를 의미합니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| const 정확성(const correctness) | 수정 가능한 경로와 불가능한 경로를 타입 수준에서 명확히 구분하는 설계 |
| 읽기 전용 인터페이스 | 데이터 조회만 허용하는 함수 집합 |

표현 비교:
| 표현 | 의미 |
|---|---|
| `const int* p` | 값 고정, 포인터 이동 가능 |
| `int* const p` | 포인터 고정, 값 수정 가능 |
| `const int& r` | 값 복사 없는 읽기 전용 참조 |

예시 코드:
```cpp
#include <iostream>
using namespace std;

class Player {
private:
    int hp_;
public:
    explicit Player(int hp) : hp_(hp) {}
    int hp() const { return hp_; } // 읽기 전용
    void damage(int d) { hp_ -= d; } // 수정 함수
};

int main() {
    const Player p(100);
    cout << p.hp() << endl;
    return 0;
}
```

예상 출력:
```text
100
```

코드 해석:
- `hp() const`는 객체 상태를 바꾸지 않는 조회 함수입니다.
- `const Player p`에서도 `hp()`는 호출 가능하지만, `damage()`는 호출할 수 없습니다.
- 이런 분리는 API 사용자의 실수를 컴파일 단계에서 줄여줍니다.

연습문제:
1. 문제 1 - const 참조 전달
   - 문제: 벡터를 `const vector<int>&`로 받아 합계를 반환하는 함수를 작성하세요.
   - 입력: 정수 벡터
   - 출력: 합계 1개
   - 조건: 함수 내부에서 벡터 수정 금지
2. 문제 2 - 조회/수정 API 분리
   - 문제: `Inventory` 클래스에 조회 함수(`const`)와 수정 함수를 분리하세요.
   - 입력: 아이템 추가/조회 요청
   - 출력: 개수/목록
   - 힌트: 멤버 함수 `const` 여부를 명확히 구분

정답 포인트:
- `const`는 문법이 아니라 설계 의도 표현 도구
- 읽기 전용 경로를 넓히면 API 안정성이 높아짐

---

[상위 문서로 돌아가기](./README.md)
