# Chapter 23 도전! 프로그래밍 3

## 학습 목표

- Chapter 01~22에서 배운 배열·포인터·문자열·구조체·`typedef`·`enum`을 문제에 적용한다.
- 여러 필드를 구조체로 묶고, 배열·함수로 조회·갱신·집계한다.
- 입력·출력 형식을 먼저 정한 뒤 작은 함수로 나누어 구현한다.

## 본문

### 23-1 이 장의 역할

이 장은 새 문법을 거의 늘리지 않습니다. **Chapter 01~22**까지 도구를 조합해 짧은 프로그램을 완성하는 연습장입니다. 쓸 수 있는 범위는 구조체, 구조체 배열, `typedef`, 중첩 구조체, `enum`, `union`, 포인터로 구조체 넘기기까지입니다.

**이 장에서 쓰지 않는 것:** 파일 입출력, `malloc`/`free` 동적 할당, 헤더 분할 빌드. 그것들은 다음 장 이후입니다. 길이가 변하는 목록이 필요하면 **고정 크기 구조체 배열**과 사용 개수 변수로 표현합니다.

| 단계 | 할 일 |
|---|---|
| 1 | 입력·출력 형식 확정 |
| 2 | 한 건 데이터를 `struct`/`typedef`로 정의 |
| 3 | 배열 + 개수로 목록 관리 |
| 4 | 조회·갱신은 함수로 분리. 필요 시 포인터 인자 |
| 5 | 기본 입력 + 경계값 테스트 |

### 23-2 구조체 배열로 목록 다루기

학생·아이템·맵 칸처럼 **같은 모양의 여러 건**은 구조체 배열이 기본입니다. `Student list[100];`과 `int count;`를 두면 `0`부터 `count - 1`까지가 유효 구간입니다. 추가할 때는 `list[count] = ...;` 후 `count++` 패턴이 흔합니다. 용량을 넘지 않는지 먼저 검사합니다.

원본을 바꾸는 함수는 `Student *p` 또는 배열 시작 주소와 인덱스를 함께 받습니다. 읽기만 하면 `const Student *p`를 쓸 수 있습니다. 상태 코드는 `enum`으로 이름을 붙이면 `switch`가 읽기 쉽습니다.

아래는 연습 전에 흐름만 확인하는 짧은 뼈대입니다. 학생 배열에서 최고 점수를 찾습니다.
```c
#include <stdio.h>

typedef struct
{
    int id;
    int score;
} Student;

int find_best_index(const Student *list, int n)
{
    int i;
    int best = 0;

    for (i = 1; i < n; i++)
    {
        if (list[i].score > list[best].score)
        {
            best = i;
        }
    }
    return best;
}

int main(void)
{
    Student list[3] = {
        {1, 70},
        {2, 95},
        {3, 88}
    };
    int idx;

    idx = find_best_index(list, 3);
    printf("%d %d\n", list[idx].id, list[idx].score);
    return 0;
}
```

**예상 출력**

```text
2 95
```

**코드 해석**

- `Student` 별칭으로 한 명 데이터를 묶습니다.
- `find_best_index`는 읽기만 하므로 `const Student *`를 받습니다.
- 최고 점수 인덱스를 돌려 `main`에서 `id`와 `score`를 출력합니다.

### 23-3 중첩 구조체와 좌표

게임·맵 연습에서는 **좌표**를 `Point`로 빼고, 캐릭터에 `Point pos`를 중첩하는 패턴이 자주 나옵니다. 이동 함수는 `void move(Point *p, int dx, int dy)`처럼 포인터로 위치를 바꿉니다. 충돌은 두 `Point`의 `x`, `y`가 같은지로 검사할 수 있습니다.

몸통이 여러 칸이면 `Point body[MAX];`와 `int length;`로 표현합니다. 연결 리스트와 `malloc`은 이 장 범위 밖입니다. 고정 배열로도 “머리 이동 + 꼬리 따라가기” 연습은 가능합니다.

아래는 머리 좌표를 한 칸 옮기고 벽 밖으로 나가는지 검사하는 최소 예입니다.

```c
#include <stdio.h>

typedef struct
{
    int x;
    int y;
} Point;

int is_out(Point p, int size)
{
    if (p.x < 0 || p.x >= size || p.y < 0 || p.y >= size)
    {
        return 1;
    }
    return 0;
}

int main(void)
{
    Point head = {9, 5};
    int size = 10;

    head.x = head.x + 1;
    if (is_out(head, size))
    {
        printf("hit wall\n");
    }
    else
    {
        printf("%d %d\n", head.x, head.y);
    }
    return 0;
}
```

**예상 출력**

```text
hit wall
```

**코드 해석**

- `head`를 오른쪽으로 한 칸 옮기면 `x`가 `10`이 됩니다.
- `size`가 `10`이면 유효 범위는 `0..9`이므로 벽입니다.
- `is_out`이 `1`을 돌려 `hit wall`을 출력합니다.

### 연습문제

**문제 1**
- 문제: `Book`을 `typedef` 구조체로 두고, 책 3권 배열에서 가격이 가장 비싼 책의 제목과 가격을 출력하세요.
- 입력: 없음. 예: `{"A", 1000}`, `{"B", 3000}`, `{"C", 2000}`
- 출력: `B 3000`
- 조건: 구조체 배열 + 반복. 최고 가격 비교 함수 분리 권장

**문제 2**
- 문제: `enum Grade { FAIL, PASS, EXCELLENT };`를 두고, 점수 `0..100`을 입력받아 `60` 미만 `FAIL`, `60` 이상 `90` 미만 `PASS`, `90` 이상 `EXCELLENT`로 판정한 뒤 이름을 출력하세요.
- 입력: 정수 점수 1개. 예: `92`
- 출력: `EXCELLENT`
- 조건: 판정 결과를 `enum`에 담고 `switch`로 문자열 출력

**문제 3**
- 문제: `Point`와 `Player { Point pos; int hp; }`를 두고, 플레이어 배열 2명의 체력 합과 첫 번째 위치를 출력하세요.
- 입력: 없음. 예: `(1,2) hp=30`, `(3,4) hp=40`
- 출력:
  ```text
  70
  1 2
  ```
- 조건: 중첩 구조체 사용. 합 계산은 함수로

**문제 4**
- 문제: `Point body[5]`와 `length`로 뱀 몸통을 표현하세요. 초기 길이 `3`, 좌표 `(2,2) (1,2) (0,2)`일 때, 머리를 오른쪽 `(3,2)`로 옮기고 몸을 한 칸씩 따라가게 한 뒤 모든 칸을 출력하세요. 꼬리 칸은 버려 길이는 `3` 유지.
- 입력: 없음
- 출력:
  ```text
  3 2
  2 2
  1 2
  ```
- 조건: 동적 할당 금지. 뒤에서 앞으로 복사한 뒤 `body[0]`에 새 머리 대입하는 방식 허용

### 정답 포인트

- 문제 1: 최고 가격 인덱스를 찾아 `title`, `price` 출력
- 문제 2: 점수 구간 → `enum` → `switch`에서 `FAIL`/`PASS`/`EXCELLENT` 문자열
- 문제 3: `sum_hp`는 `const Player *`와 개수. 위치는 `players[0].pos.x` 등
- 문제 4: `for (i = length - 1; i > 0; i--) body[i] = body[i - 1];` 후 `body[0] = new_head;`
- 공통: 이 장은 구조체까지. `malloc`·파일 I/O 없이 배열·포인터·`enum`으로 해결

---

[상위 문서로 돌아가기](./README.md)
