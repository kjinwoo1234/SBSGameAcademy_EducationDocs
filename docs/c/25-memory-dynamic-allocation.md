# Chapter 25 메모리 관리와 동적 할당

## 학습 목표
- 프로그램 메모리 구조를 이해한다.
- 동적 할당과 해제의 안전 수칙을 익힌다.

## 세부 주제
### 25-1 메모리 구조
- 코드/데이터/힙/스택 영역

### 25-2 동적 할당
- `malloc`, `calloc`, `realloc`, `free`

## 실습 체크리스트
- 동적 배열을 확장하며 데이터를 저장하는 코드를 작성한다.

## 본문

### 25-1 메모리 구조
- 코드 영역: 실행 명령
- 데이터 영역: 전역/정적 변수
- 스택 영역: 지역변수, 함수 호출 정보
- 힙 영역: 동적 할당 메모리

### 25-2 동적 할당
- `malloc`: 크기만큼 메모리 할당
- `calloc`: 개수*크기 할당 + 0 초기화
- `realloc`: 기존 메모리 크기 변경
- `free`: 할당 메모리 해제

예시 코드:
```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int n = 3;
    int *arr = (int *)malloc(sizeof(int) * n);
    if (!arr) return 1;

    for (int i = 0; i < n; i++) arr[i] = (i + 1) * 10;
    for (int i = 0; i < n; i++) printf("%d ", arr[i]);
    printf("\n");

    free(arr);
    arr = NULL;
    return 0;
}
```

연습문제:
1. 사용자 입력 크기만큼 동적 배열 생성 후 합계 출력
2. `realloc`으로 배열 크기를 2배로 늘려 값 추가

정답 포인트:
- 할당 직후 `NULL` 체크
- `free` 후 포인터 `NULL` 처리

---

[상위 문서로 돌아가기](./README.md)
