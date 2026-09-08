# Chapter 20 문자/문자열 관련 함수

## 학습 목표

- 문자 단위·문자열 단위 입출력 함수를 구분해 쓴다.
- 입력 버퍼에 남은 개행이 다음 읽기에 영향을 줄 수 있음을 이해한다.
- `strlen`, `strcpy`, `strcat`, `strcmp`로 길이를 재고 복사·연결·비교한다.

## 본문

### 20-1 스트림과 문자 단위 입출력

표준 입력·표준 출력·표준 오류는 프로그램과 콘솔 사이를 잇는 **스트림**입니다. 이름은 `stdin`, `stdout`, `stderr`입니다. 대부분의 콘솔 입출력은 이 통로를 지나갑니다.

**`getchar`**는 표준 입력에서 문자 하나를 읽고, **`putchar`**는 문자 하나를 씁니다. 한 글자씩 살펴보거나 간단히 메아리칠 때 씁니다. 더 이상 읽을 것이 없으면 `getchar`는 `EOF`를 돌려줍니다. 지금은 한 글자 읽기·쓰기 흐름만 익힙니다.

| 함수 | 역할 |
|---|---|
| `getchar` | 문자 하나 입력 |
| `putchar` | 문자 하나 출력 |

아래를 실행해 입력한 첫 글자가 그대로 나오는지 확인해 보세요.

```c
#include <stdio.h>

int main(void)
{
    int ch;

    ch = getchar();
    if (ch != EOF)
    {
        putchar(ch);
        putchar('\n');
    }
    return 0;
}
```

**예상 출력** — 입력 `A` 후 Enter

```text
A
```

**코드 해석**

- `getchar`가 첫 글자를 `ch`에 담습니다.
- `putchar(ch)`로 같은 글자를 출력합니다.
- Enter로 넣은 개행은 버퍼에 남을 수 있습니다. 다음 절에서 이어집니다.

### 20-2 `fgets`와 `puts`

**`fgets`**는 한 줄을 읽되 최대 길이를 정할 수 있습니다. `fgets(buf, sizeof(buf), stdin);`처럼 씁니다. 공백이 섞인 문장을 받을 때 `scanf("%s")`보다 안전한 경우가 많습니다. **`puts`**는 문자열을 출력한 뒤 줄바꿈을 자동으로 붙입니다.

입력은 내부 **버퍼**를 거친 뒤 함수에 전달되는 경우가 많습니다. 이전 입력에서 남은 개행 때문에 다음 `getchar`나 `scanf`가 바로 끝나 버리는 일이 잦습니다.

| 용어 | 의미 |
|---|---|
| 버퍼 | 입력·출력을 잠시 모아 두는 메모리 공간 |
| `fgets` | 최대 길이를 지키며 한 줄을 읽는 함수 |
| `puts` | 문자열 출력 후 줄바꿈을 붙이는 함수 |

### 20-3 줄 끝 개행 제거

`fgets`로 읽은 줄 끝의 `\n`은 문자열에 포함될 수 있습니다. 길이를 재거나 비교하기 전에 제거하는 습관을 들입니다.

`strcspn(문자열, "찾을문자들")`은 앞에서부터 보아 두 번째 인자에 있는 글자가 **나오기 직전**까지의 길이를 돌려줍니다. 그래서 `line[strcspn(line, "\n")] = '\0';`는 처음 나오는 `\n` 자리에 끝 표시 `\0`을 넣어 개행을 끊습니다.

아래를 실행해 한 줄을 읽고 다시 출력해 보세요. 입력 예는 `hello`입니다.

```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char line[64];

    if (fgets(line, sizeof(line), stdin) != NULL)
    {
        line[strcspn(line, "\n")] = '\0';
        puts(line);
    }
    return 0;
}
```

**예상 출력** — 입력 `hello`

```text
hello
```

**코드 해석**

- `fgets`가 최대 `sizeof(line) - 1`글자까지 읽습니다.
- `strcspn(line, "\n")`으로 개행 위치를 찾아 `\0`으로 바꿉니다.
- `puts`가 내용을 출력하고 줄바꿈을 붙입니다.

### 20-4 `strlen`과 `strcmp`

**`strlen`**은 널 문자 앞까지의 글자 수를 구합니다. 배열 칸 전체가 아니라 **문자열 길이**입니다. **`strcmp`**는 두 문자열을 사전 순으로 비교합니다. 같으면 `0`, 첫 번째가 더 작으면 음수, 더 크면 양수를 돌려주는 경우가 일반적입니다. 포인터 주소가 같은지 보는 `==`와 혼동하지 마세요. **내용**이 같은지는 `strcmp`가 `0`인지로 판단합니다.

| 함수 | 용도 |
|---|---|
| `strlen` | 문자열 길이 |
| `strcmp` | 문자열 내용 비교 |

아래를 실행해 길이와 비교 결과를 확인해 보세요. 입력 예는 첫 줄 `abc`, 둘째 줄 `abd`입니다.

```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char a[30];
    char b[30];

    fgets(a, sizeof(a), stdin);
    fgets(b, sizeof(b), stdin);

    a[strcspn(a, "\n")] = '\0';
    b[strcspn(b, "\n")] = '\0';

    printf("len(a): %zu\n", strlen(a));
    printf("compare: %d\n", strcmp(a, b));
    return 0;
}
```

**예상 출력** — 입력 `abc` / `abd`

```text
len(a): 3
compare: -1
```

**코드 해석**

- 개행을 제거한 뒤 `strlen(a)`는 `3`입니다.
- `strcmp("abc", "abd")`는 앞에서부터 비교하다 다른 글자에서 음수가 됩니다. 환경에 따라 `-1`이 아닌 다른 음수일 수도 있습니다.
- 같음 여부만 필요하면 `strcmp(a, b) == 0`을 씁니다.

### 20-5 `strcpy`와 `strcat`

**`strcpy`**는 원본 문자열을 대상 배열에 복사합니다. **`strcat`**는 대상 끝의 널 문자 위치에 원본을 이어 붙입니다. 둘 다 대상 배열에 **남는 칸**이 충분한지 먼저 확인해야 합니다. 칸을 넘기면 인접 메모리를 망가뜨리는 **오버플로우**로 이어질 수 있습니다. 대상은 쓰기 가능한 `char` 배열이어야 하며, 문자열 리터럴을 가리키는 포인터에 복사하면 안 됩니다.

| 함수 | 용도 | 주의 |
|---|---|---|
| `strcpy` | 문자열 복사 | 대상 크기 확인 |
| `strcat` | 문자열 이어붙이기 | 대상 남은 칸 확인 |
| 오버플로우 | 저장 공간을 넘어 데이터가 넘침 | 길이·용량을 함께 계산 |

아래를 실행해 복사와 연결 결과를 확인해 보세요.

```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char dest[32];
    char hello[16] = "Hello";
    char world[16] = "World";

    strcpy(dest, hello);
    strcat(dest, " ");
    strcat(dest, world);

    printf("%s\n", dest);
    printf("%zu\n", strlen(dest));
    return 0;
}
```

**예상 출력**

```text
Hello World
11
```

**코드 해석**

- `strcpy(dest, hello)`로 `"Hello"`를 복사합니다.
- `strcat`로 공백과 `"World"`를 이어 붙입니다.
- `strlen(dest)`는 공백 포함 `11`입니다. 널 문자는 길이에 넣지 않습니다.

### 연습문제

**문제 1**
- 문제: 두 문자열을 입력받아 각각의 길이를 출력하세요.
- 입력: 한 줄씩 문자열 2개. 공백 포함 가능. 예: `hi there` / `c`
- 출력:
  ```text
  8
  1
  ```
- 조건: `fgets`로 읽고 개행 제거 후 `strlen`

**문제 2**
- 문제: 두 문자열이 같으면 `same`, 다르면 `different`를 출력하세요.
- 입력: 문자열 2줄. 예: `apple` / `apple`
- 출력: `same`
- 조건: 포인터 `==` 비교 금지. `strcmp` 결과가 `0`인지 확인

**문제 3**
- 문제: 빈 대상 배열에 첫 문자열을 복사한 뒤, 공백 하나와 둘째 문자열을 이어 붙여 출력하세요.
- 입력: 없음. 예로 `"Game"`과 `"Dev"`를 사용
- 출력: `Game Dev`
- 조건: `strcpy`, `strcat` 사용. 대상 배열 크기는 충분히 크게

### 정답 포인트

- 문제 1: `fgets` → 개행을 `\0`으로 → `printf("%zu\n", strlen(...));`
- 문제 2: `if (strcmp(a, b) == 0) puts("same"); else puts("different");`
- 문제 3: `strcpy(dest, a); strcat(dest, " "); strcat(dest, b);`
- 공통: `fgets` 뒤 개행 정리, 내용 비교는 `strcmp`, 복사·연결 전 대상 용량 확인

---

[상위 문서로 돌아가기](./README.md)
