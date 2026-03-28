# C 제어문

제어문은 프로그램 실행 흐름을 결정합니다.  
입문 단계에서 제어문을 정확히 익히면 이후 알고리즘/자료구조 학습이 수월해집니다.

## 1) 조건문 `if`, `else if`, `else`
조건식이 참일 때만 특정 블록을 실행합니다.

```c
int score = 87;
if (score >= 90) {
    printf("A\n");
} else if (score >= 80) {
    printf("B\n");
} else {
    printf("C\n");
}
```

## 2) 선택문 `switch`
정수/문자 기반 분기에서 가독성이 좋습니다.  
각 `case` 끝에 `break`를 넣지 않으면 다음 `case`로 이어집니다.

```c
int menu = 2;
switch (menu) {
    case 1: printf("조회\n"); break;
    case 2: printf("등록\n"); break;
    default: printf("기타\n"); break;
}
```

## 3) 반복문 `for`
반복 횟수가 명확할 때 사용합니다.

```c
for (int i = 1; i <= 5; i++) {
    printf("%d ", i);
}
```

## 4) 반복문 `while`, `do-while`
- `while`: 조건 검사 후 실행
- `do-while`: 최소 1회 실행 후 조건 검사

```c
int n = 1;
while (n <= 3) {
    printf("%d\n", n);
    n++;
}
```

## 5) 흐름 제어 `break`, `continue`
- `break`: 반복문 즉시 종료
- `continue`: 현재 반복을 건너뛰고 다음 반복 진행

```c
for (int i = 1; i <= 10; i++) {
    if (i == 3) continue;
    if (i == 8) break;
    printf("%d ", i);
}
```

## 6) 무한 루프 주의
반복 변수 갱신을 빼먹으면 무한 루프가 발생합니다.

## 7) 연습 문제
1. 1부터 100까지 짝수만 출력하세요.
2. 구구단 2단~9단을 중첩 반복문으로 출력하세요.
3. 메뉴 선택 프로그램을 `switch`로 작성하세요.

---

[상위 문서로 돌아가기](./README.md)
