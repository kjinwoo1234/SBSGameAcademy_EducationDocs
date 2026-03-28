# [C 자습 자료] 05. 포인터와 동적 메모리

## 1. 포인터 기초
포인터는 변수의 주소를 저장합니다.

```c
int n = 10;
int* p = &n;
printf("%d\n", *p);
```

---

## 2. 포인터와 배열
```c
int arr[3] = {10, 20, 30};
int* p = arr;
printf("%d\n", *(p + 1)); // 20
```

---

## 3. 동적 메모리
`malloc()`으로 할당하고 `free()`로 해제합니다.

```c
#include <stdlib.h>

int* p = (int*)malloc(sizeof(int));
if (p != NULL) {
    *p = 5;
    printf("%d\n", *p);
    free(p);
    p = NULL;
}
```
