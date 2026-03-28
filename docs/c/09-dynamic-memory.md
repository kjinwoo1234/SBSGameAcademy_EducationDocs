# C 동적 메모리

정적 배열은 크기를 컴파일 시점에 정해야 하지만, 동적 메모리는 실행 중 필요 크기만큼 할당할 수 있습니다.  
데이터 크기를 미리 알 수 없는 프로그램(입력 기반 처리, 자료구조 구현)에 필수입니다.

## 1) `malloc`과 `free`
- `malloc(size)`: 힙(Heap)에서 메모리 확보
- `free(ptr)`: 사용이 끝난 메모리 반납

```c
#include <stdlib.h>
#include <stdio.h>

int* p = (int*)malloc(sizeof(int));
if (p != NULL) {
    *p = 5;
    printf("%d\n", *p);
    free(p);
    p = NULL; // 해제 후 초기화
}
```

## 2) 배열 동적 할당
```c
int n = 5;
int* arr = (int*)malloc(sizeof(int) * n);
if (arr == NULL) return;

for (int i = 0; i < n; i++) arr[i] = (i + 1) * 10;
for (int i = 0; i < n; i++) printf("%d ", arr[i]);

free(arr);
arr = NULL;
```

## 3) `calloc`, `realloc`
- `calloc(count, size)`: 0으로 초기화된 메모리 할당
- `realloc(ptr, newSize)`: 기존 메모리 크기 재조정

```c
int* nums = (int*)calloc(3, sizeof(int)); // [0,0,0]
nums = (int*)realloc(nums, sizeof(int) * 6);
free(nums);
```

## 4) 메모리 오류 유형
- 메모리 누수(할당 후 `free` 누락)
- 이중 해제(Double Free)
- 해제 후 사용(Use-After-Free)

## 5) 안전 수칙
1. 할당 직후 `NULL` 검사
2. 할당/해제를 함수 단위로 짝지어 관리
3. 해제 후 포인터를 `NULL`로 초기화

## 6) 연습 문제
1. `n`개 정수를 동적 배열로 받아 평균 계산
2. `realloc`으로 배열 크기를 2배 확장
3. 구조체 배열을 동적 할당해 이름/점수 관리

---

[상위 문서로 돌아가기](./README.md)
