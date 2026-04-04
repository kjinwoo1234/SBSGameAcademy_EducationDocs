# Chapter 19 함수 포인터와 `void` 포인터

## 학습 목표
- 함수 포인터 선언과 호출 문법을 익힌다.
- `void*`의 유연성과 주의점을 이해한다.

## 세부 주제
### 19-1 함수 포인터 / `void` 포인터
- 콜백 구조와 범용 포인터 사용

### 19-2 `main` 함수 인자 전달
- 명령행 인자(`argc`, `argv`) 기초

## 실습 체크리스트
- 계산기 연산을 함수 포인터 배열로 구현한다.

## 본문

### 19-1 함수 포인터 / `void` 포인터
- 함수 포인터는 "함수의 주소를 저장하는 포인터"입니다.
- 메뉴 선택에 따라 다른 함수를 호출할 때 유용합니다.
- `void*`는 어떤 타입 주소든 받을 수 있지만, 사용할 때는 적절한 타입으로 변환해야 합니다.

### 19-2 `main` 함수 인자 전달
- 명령행 인자 `argc`, `argv`를 사용하면 실행 시 외부 값을 전달받을 수 있습니다.
- 예: `program.exe hello` -> `argv[1]`이 `"hello"`

예시 코드:
```c
#include <stdio.h>

int add(int a, int b) { return a + b; }
int sub(int a, int b) { return a - b; }

int main(int argc, char *argv[]) {
    int (*op[2])(int, int) = {add, sub};
    printf("add: %d\n", op[0](7, 3));
    printf("sub: %d\n", op[1](7, 3));
    printf("argc: %d\n", argc);
    return 0;
}
```

연습문제:
1. 곱셈/나눗셈 함수를 추가해 함수 포인터 배열 확장
2. 명령행 인자 개수를 출력해보세요.

정답 포인트:
- 함수 포인터 선언 형태: `반환형 (*이름)(매개변수)`
- `argv`는 문자열 배열

---

[상위 문서로 돌아가기](./README.md)
