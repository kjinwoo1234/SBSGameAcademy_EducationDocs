# C 파일 입출력

## 핵심
- 파일 열기: `fopen()`
- 파일 쓰기/읽기: `fprintf()`, `fscanf()`
- 파일 닫기: `fclose()`

## 예제
```c
FILE* fp = fopen("score.txt", "w");
if (fp != NULL) {
    fprintf(fp, "%d\n", 100);
    fclose(fp);
}
```

---

[상위 문서로 돌아가기](./README.md)
