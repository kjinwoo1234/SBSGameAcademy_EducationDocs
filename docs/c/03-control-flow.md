# [C 자습 자료] 03. 제어문

## 1. 조건문
`if`, `else if`, `else`, `switch`를 사용합니다.

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

---

## 2. 반복문
`for`, `while`, `do-while`

```c
for (int i = 1; i <= 5; i++) {
    printf("%d ", i);
}
```

---

## 3. 흐름 제어
- `break`: 반복문 즉시 종료
- `continue`: 다음 반복으로 진행
