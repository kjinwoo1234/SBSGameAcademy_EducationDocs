# C 동적 메모리

## 핵심
- `malloc()`으로 메모리 할당
- `free()`로 메모리 해제

## 예제
```c
int* p = (int*)malloc(sizeof(int));
if (p != NULL) {
    *p = 5;
    free(p);
    p = NULL;
}
```

---

[상위 문서로 돌아가기](./README.md)
