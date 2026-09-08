# Chapter 25 메모리 관리와 동적 할당

## 학습 목표

- 코드·데이터·스택·힙 영역의 역할을 구분한다.
- `malloc`·`calloc`·`realloc`·`free`로 힙 메모리를 잡고 돌려준다.
- 할당 실패 검사와 `free` 후 포인터 다루기 안전 수칙을 익힌다.

## 본문

### 25-1 프로그램 메모리 영역

실행 중인 프로그램이 쓰는 메모리는 역할별로 나누어 이해할 수 있습니다. **코드 영역**은 실행할 명령입니다. **데이터 영역**은 전역·정적 변수처럼 프로그램 동안 살아 있는 값이 둡니다. **스택**은 함수가 호출될 때 지역 변수와 복귀 정보가 쌓이고, 함수가 끝나면 사라집니다. **힙**은 `malloc` 계열로 **실행 중에 크기**를 정해 잡는 공간입니다. 다 쓰면 `free`로 돌려줍니다.

지역 배열 `int a[100];`은 스택에 두고, 크기를 입력받은 뒤에야 칸 수를 정하려면 힙이 맞습니다.

| 영역 | 생성 | 소멸 |
|---|---|---|
| 코드 | 프로그램 로드 | 종료 |
| 데이터 | 시작 시 | 종료 |
| 스택 | 함수 호출 | 함수 종료 |
| 힙 | `malloc` 등 | `free` |

### 25-2 `malloc`과 `free`

**`malloc`**은 요청한 **바이트 수**만큼 힙을 잡고, 그 시작 주소를 `void *`로 돌려줍니다. 보통 `(int *)malloc(sizeof(int) * n)`처럼 쓸 타입 포인터로 변환해 씁니다. 내용은 초기화되지 않을 수 있습니다. 실패하면 `NULL`입니다. 반드시 검사합니다.

**`free`**는 더 이상 쓰지 않는 블록을 돌려줍니다. `malloc`으로 받은 주소만 `free`합니다. 같은 주소를 두 번 `free`하면 안 됩니다. `free` 뒤 그 포인터로 읽거나 쓰면 위험합니다. 습관으로 `free(p); p = NULL;`을 많이 씁니다.

| 함수 | 역할 |
|---|---|
| `malloc` | 바이트만큼 할당. 초기화 없음 |
| `free` | 힙 반환 |

아래를 실행해 `10 20 30`이 출력되는지 확인해 보세요.

```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int n = 3;
    int *arr;
    int i;

    arr = (int *)malloc(sizeof(int) * n);
    if (arr == NULL)
    {
        printf("alloc failed\n");
        return 1;
    }

    for (i = 0; i < n; i++)
    {
        arr[i] = (i + 1) * 10;
    }
    for (i = 0; i < n; i++)
    {
        printf("%d ", arr[i]);
    }
    printf("\n");

    free(arr);
    arr = NULL;
    return 0;
}
```

**예상 출력**

```text
10 20 30
```

**코드 해석**

- `sizeof(int) * n` 바이트를 힙에 잡습니다.
- `arr[i]`로 배열처럼 쓰고, 끝나면 `free`합니다.
- `arr = NULL`로 dangling 포인터 사용을 줄입니다.

### 25-3 `calloc`과 `realloc`

**`calloc`**은 개수와 한 요소 크기를 받아 `개수 * 크기`만큼 잡고, 보통 **0으로 채웁니다**. `calloc(n, sizeof(int))`처럼 씁니다. 초기값이 0이면 편한 경우가 많습니다.

**`realloc`**은 이미 잡은 블록 크기를 바꿀 때 씁니다. 새 포인터를 돌려주며, 실패하면 `NULL`이고 **기존 블록은 그대로**인 경우가 일반적입니다. 그래서 `arr = realloc(arr, new_size);`처럼 바로 덮으면 실패 시 원래 주소를 잃습니다. 임시 포인터에 받은 뒤 성공일 때만 대입합니다.

| 함수 | 특징 |
|---|---|
| `calloc` | 0 초기화에 가깝음 |
| `realloc` | 크기 변경. 실패 시 기존 포인터 보존 주의 |

아래를 실행해 늘린 배열 전체가 출력되는지 확인해 보세요.

```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int *arr;
    int *tmp;
    int i;

    arr = (int *)malloc(sizeof(int) * 2);
    if (arr == NULL)
    {
        return 1;
    }
    arr[0] = 1;
    arr[1] = 2;

    tmp = (int *)realloc(arr, sizeof(int) * 4);
    if (tmp == NULL)
    {
        free(arr);
        return 1;
    }
    arr = tmp;
    arr[2] = 3;
    arr[3] = 4;

    for (i = 0; i < 4; i++)
    {
        printf("%d ", arr[i]);
    }
    printf("\n");

    free(arr);
    return 0;
}
```

**예상 출력**

```text
1 2 3 4
```

**코드 해석**

- 처음 2칸을 잡은 뒤 `realloc`으로 4칸으로 늘립니다.
- 실패하면 `tmp`만 `NULL`이고 `arr`는 이전 블록을 가리키므로 `free(arr)`로 정리합니다.
- 성공 시 `arr = tmp` 후 새 칸에 값을 넣습니다.

### 25-4 동적 배열과 구조체

크기를 입력받은 뒤 배열을 만들 때 힙이 필요합니다. 구조체도 `Student *list = malloc(sizeof(Student) * n);`처럼 잡을 수 있습니다. 사용이 끝나면 `free(list);` 한 번으로 블록 전체를 반환합니다. 구조체 안에 **또 다른 `malloc` 포인터**가 있으면 그 안쪽을 먼저 `free`하는 식으로 짝을 맞춥니다. 이 절에서는 한 블록 배열만 다룹니다.

연결 리스트처럼 노드마다 `malloc`하는 패턴은 같은 규칙입니다. 만든 만큼 `free`합니다.

아래를 실행해 입력 합이 나오는지 확인해 보세요. 입력 예는 `3` 다음 `10 20 30`입니다.

```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int n;
    int *arr;
    int i;
    int sum = 0;

    if (scanf("%d", &n) != 1 || n <= 0)
    {
        return 1;
    }

    arr = (int *)malloc(sizeof(int) * n);
    if (arr == NULL)
    {
        return 1;
    }

    for (i = 0; i < n; i++)
    {
        scanf("%d", &arr[i]);
        sum = sum + arr[i];
    }
    printf("%d\n", sum);

    free(arr);
    return 0;
}
```

**예상 출력** — 입력 `3` / `10 20 30`

```text
60
```

**코드 해석**

- `n`을 받은 뒤에야 `malloc` 크기를 정합니다.
- 합을 구한 뒤 `free`합니다.
- `n`이 잘못되면 할당하지 않고 끝냅니다.

### 연습문제

**문제 1**
- 문제: 크기 `n`과 정수 `n`개를 입력받아 동적 배열에 저장한 뒤 합을 출력하세요.
- 입력: 첫 줄 `n`, 둘째 줄 정수 `n`개. 예: `4` / `1 2 3 4`
- 출력: `10`
- 조건: `malloc` 후 `NULL` 검사. 끝에 `free`. `1 <= n <= 1000`

**문제 2**
- 문제: `calloc`으로 정수 `5`칸을 만든 뒤, 인덱스 `0..4`에 `i + 1`을 넣고 모두 출력하세요.
- 입력: 없음
- 출력: `1 2 3 4 5`
- 조건: `malloc` 대신 `calloc` 사용

**문제 3**
- 문제: 길이 `2` 배열 `{7, 8}`을 `malloc`한 뒤 `realloc`으로 `4`칸으로 늘리고 `9`, `10`을 추가해 출력하세요.
- 입력: 없음
- 출력: `7 8 9 10`
- 조건: `realloc` 결과는 임시 포인터로 받기. 실패 시 기존 블록 `free`

### 정답 포인트

- 문제 1: `malloc(sizeof(int) * n)` → 입력 루프 → 합 → `free`
- 문제 2: `calloc(5, sizeof(int));` 후 대입·출력
- 문제 3: `tmp = realloc(arr, sizeof(int) * 4);` 성공 시에만 `arr = tmp`
- 공통: 실패 검사, `free` 짝, `free` 후 재사용 금지

---

[상위 문서로 돌아가기](./README.md)
