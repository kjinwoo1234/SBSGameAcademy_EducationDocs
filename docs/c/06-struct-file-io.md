# [C 자습 자료] 06. 구조체와 파일 입출력

## 1. 구조체
```c
typedef struct {
    char name[20];
    int score;
} Student;
```

---

## 2. 파일 입출력
`fopen`, `fprintf`, `fscanf`, `fclose`를 사용합니다.

```c
FILE* fp = fopen("score.txt", "w");
if (fp != NULL) {
    fprintf(fp, "%d\n", 100);
    fclose(fp);
}
```

---

## 체크 포인트
- `scanf()`에서 주소 연산자 `&`를 올바르게 사용하는가?
- 포인터와 배열의 관계를 설명할 수 있는가?
- `malloc/free`를 짝으로 안전하게 사용하는가?
