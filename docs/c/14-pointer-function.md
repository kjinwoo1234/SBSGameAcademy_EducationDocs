# Chapter 14 포인터와 함수

## 학습 목표
- 함수 인자로 포인터/배열을 전달하는 이유를 이해한다.
- 값 전달과 참조 효과 전달을 구분할 수 있다.

## 세부 주제
### 14-1 함수 인자로 배열 전달
- 배열 길이 전달과 접근 패턴

### 14-2 Call-by-value vs Call-by-reference
- C에서의 참조 효과 구현 방법

### 14-3 포인터 대상 `const`
- 수정 가능성 제한과 API 안정성

## 실습 체크리스트
- 포인터 인자를 사용해 swap 함수를 구현한다.

## 본문

### 14-1 함수 인자로 배열 전달
- C에서 배열을 함수 인자로 넘기면 첫 원소 주소가 전달됩니다.
- 그래서 배열 길이는 별도 인자로 함께 전달해야 안전합니다.

### 14-2 Call-by-value vs Call-by-reference
- C의 기본 전달 방식은 값 전달(Call-by-value)입니다.
- 원본 값을 바꾸려면 주소를 전달해 참조 효과를 만들어야 합니다.

### 14-3 포인터 대상 `const`
- `const int *p`는 "포인터로 가리키는 값 수정 금지"를 의미합니다.
- 읽기 전용 데이터 보호에 유용합니다.

예시 코드:
```c
#include <stdio.h>

void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int sum_array(const int *arr, int n) {
    int i, sum = 0;
    for (i = 0; i < n; i++) sum += arr[i];
    return sum;
}

int main(void) {
    int x = 3, y = 7;
    int nums[3] = {1, 2, 3};
    swap(&x, &y);
    printf("%d %d\n", x, y);
    printf("sum: %d\n", sum_array(nums, 3));
    return 0;
}
```

연습문제:
1. 포인터 인자로 최소값/최대값을 교환하는 함수를 작성하세요.
2. 배열 평균을 구하는 함수를 작성하세요.

정답 포인트:
- 원본 변경 함수는 포인터 인자 사용
- 배열 함수는 `배열 + 길이` 같이 전달

---

[상위 문서로 돌아가기](./README.md)
