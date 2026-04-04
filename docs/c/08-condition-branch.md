# Chapter 08 조건문과 분기

## 학습 목표
- 조건문과 분기문의 차이를 이해한다.
- 흐름 제어 키워드를 적절히 사용할 수 있다.

## 세부 주제
### 08-1 조건적 실행과 분기
- `if`, `else if`, `else` 구성

### 08-2 `continue` / `break`
- 반복 흐름 제어와 탈출

### 08-3 `switch`와 `goto`
- 다중 분기와 레이블 기반 점프의 주의점

## 실습 체크리스트
- 같은 분기 로직을 `if`와 `switch`로 각각 구현한다.

## 본문

### 08-1 조건적 실행과 분기
- 조건문은 "상황에 따라 다른 코드 실행"을 가능하게 합니다.
- `if`, `else if`, `else`를 사용해 복수 조건을 단계적으로 검사할 수 있습니다.
- 조건식을 명확하게 작성하면 버그를 줄이고 코드 가독성이 좋아집니다.

### 08-2 `continue` / `break`
- `continue`는 현재 반복의 남은 코드를 건너뛰고 다음 반복으로 넘어갑니다.
- `break`는 반복문 또는 `switch`를 즉시 종료합니다.
- 두 키워드는 흐름 제어에 강력하지만, 과도하게 사용하면 코드 이해가 어려워질 수 있습니다.

### 08-3 `switch`와 `goto`
- `switch`는 하나의 값에 대해 여러 경우를 분기할 때 읽기 쉽고 관리가 편합니다.
- 각 `case` 끝에서 `break`를 넣지 않으면 다음 `case`로 이어질 수 있으므로 주의가 필요합니다.
- `goto`는 코드를 특정 레이블로 즉시 이동시키며, 남용 시 코드 흐름이 복잡해질 수 있어 학습용/제한적 용도로 이해하는 것이 좋습니다.

예시 코드:
```c
#include <stdio.h>

int main(void) {
    int score = 85;

    if (score >= 90) printf("A\n");
    else if (score >= 80) printf("B\n");
    else printf("C 이하\n");

    int menu = 2;
    switch (menu) {
        case 1: printf("시작\n"); break;
        case 2: printf("설정\n"); break;
        default: printf("종료\n"); break;
    }
    return 0;
}
```

연습문제:
1. 점수(`0~100`)를 입력받아 A/B/C/F를 출력하세요.
2. 숫자 1~3을 입력받아 각 메뉴 이름을 `switch`로 출력하세요.
3. 반복문에서 3의 배수는 건너뛰고(`continue`), 20 이상이면 종료(`break`)하는 코드를 작성하세요.

정답 포인트:
- 등급 분기: `if (score >= 90) ... else if ...`
- 메뉴 분기: `switch (menu) { case ... }`
- 흐름 제어: `continue`, `break` 위치를 반복문 내부에 배치

---

[상위 문서로 돌아가기](./README.md)
