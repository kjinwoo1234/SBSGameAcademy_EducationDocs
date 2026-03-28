# [C 자습 자료] 04. 배열, 문자열, 함수

## 1. 배열
```c
int nums[5] = {10, 20, 30, 40, 50};
for (int i = 0; i < 5; i++) {
    printf("%d ", nums[i]);
}
```

---

## 2. 문자열
C 문자열은 `char` 배열이며 끝에 `\0`이 포함됩니다.

```c
char name[] = "Hong";
printf("%s\n", name);
```

---

## 3. 함수
```c
int Add(int a, int b) {
    return a + b;
}
```
