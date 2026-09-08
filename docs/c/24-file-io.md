# Chapter 24 파일 입출력

## 학습 목표

- `FILE *` 스트림으로 파일을 열고, 읽고 쓰고, 닫는 흐름을 익힌다.
- 텍스트 모드와 바이너리 모드, 대표 입출력 함수를 구분한다.
- `fseek`·`ftell`·`rewind`로 파일 위치 지시자를 다룬다.

## 본문

### 24-1 파일 스트림과 기본 흐름

디스크 파일도 콘솔처럼 **스트림**으로 다룹니다. 타입은 `FILE *`입니다. 기본 흐름은 **열기 → 읽거나 쓰기 → 닫기**입니다. 열기에 실패하면 `fopen`은 `NULL`을 돌려줍니다. 실패를 검사하지 않으면 이후 호출이 위험합니다. 사용이 끝나면 `fclose`로 닫아 버퍼를 비우고 자원을 돌려줍니다.

표준 입출력 `stdin`/`stdout`도 `FILE *` 계열입니다. 파일은 경로 문자열로 엽니다.

| 용어 | 의미 |
|---|---|
| 파일 스트림 | 파일과 프로그램 사이 데이터 통로 |
| `FILE *` | 열린 스트림을 가리키는 포인터 |
| 파일 위치 지시자 | 지금 읽거나 쓸 위치 |

아래를 실행한 뒤 같은 폴더에 `score.txt`가 생기고 내용이 `Kim 95`인지 확인해 보세요.

```c
#include <stdio.h>

int main(void)
{
    FILE *fp;

    fp = fopen("score.txt", "w");
    if (fp == NULL)
    {
        printf("open failed\n");
        return 1;
    }

    fprintf(fp, "Kim 95\n");
    fclose(fp);
    printf("saved\n");
    return 0;
}
```

**예상 출력**

```text
saved
```

**코드 해석**

- `"w"`로 쓰기용으로 엽니다. 파일이 없으면 만들고, 있으면 내용을 비우는 쪽에 가깝습니다.
- `fprintf`는 `printf`와 비슷하지만 첫 인자가 파일 스트림입니다.
- `fclose`로 닫은 뒤 콘솔에 `saved`를 알립니다.

### 24-2 개방 모드

모드 문자열은 읽기·쓰기·추가와 텍스트·바이너리를 정합니다. `"r"`은 읽기 전용입니다. 파일이 없으면 실패합니다. `"w"`는 쓰기입니다. `"a"`는 끝에서 이어 붙입니다. 바이너리는 `"rb"`, `"wb"`, `"ab"`처럼 `b`를 붙입니다. 읽으면서 쓰려면 `"r+"` 등 조합이 있습니다. 입문에서는 `r`/`w`/`a`와 `rb`/`wb`부터 익힙니다.

| 모드 | 의미 |
|---|---|
| `"r"` | 읽기. 없으면 실패 |
| `"w"` | 쓰기. 기존 내용 삭제에 가깝음 |
| `"a"` | 끝에 추가 |
| `"rb"`, `"wb"` | 바이너리 읽기·쓰기 |

### 24-3 텍스트 읽기·쓰기 함수

텍스트에서는 `fprintf`/`fscanf`, `fputs`/`fgets`를 자주 씁니다. `fgets`는 최대 길이를 지키며 한 줄을 읽습니다. 파일 끝이면 `NULL`을 돌려줍니다. 콘솔에서 배운 버퍼·개행 정리 습관이 그대로 도움이 됩니다.

한 글자 단위는 `fgetc`/`fputc`입니다. 더 이상 읽을 것이 없으면 `EOF`입니다.

| 함수 | 역할 |
|---|---|
| `fprintf` / `fscanf` | 형식 지정 쓰기·읽기 |
| `fputs` / `fgets` | 문자열·줄 단위 |
| `fgetc` / `fputc` | 문자 단위 |

아래를 실행하려면 먼저 `score.txt`에 `Kim 95`가 있어야 합니다. 앞 절 예제를 한 번 실행한 뒤 이 코드를 실행해 보세요.

```c
#include <stdio.h>

int main(void)
{
    FILE *fp;
    char name[32];
    int score;

    fp = fopen("score.txt", "r");
    if (fp == NULL)
    {
        printf("open failed\n");
        return 1;
    }

    if (fscanf(fp, "%31s %d", name, &score) == 2)
    {
        printf("%s %d\n", name, score);
    }
    fclose(fp);
    return 0;
}
```

**예상 출력**

```text
Kim 95
```

**코드 해석**

- `"r"`로 읽어 엽니다.
- `fscanf`가 이름과 점수를 변수에 담습니다. 성공 시 읽은 항목 수 `2`를 돌려줍니다.
- 콘솔 `printf`로 확인한 뒤 `fclose`합니다.

### 24-4 바이너리 `fread` / `fwrite`

바이너리 모드는 바이트를 **그대로** 읽고 씁니다. 구조체나 `int` 배열을 통째로 저장할 때 `fwrite`/`fread`를 씁니다. 크기와 개수를 인자로 넘깁니다. `fwrite(ptr, sizeof(타입), 개수, fp);` 형태가 흔합니다.

플랫폼·패딩 때문에 구조체 바이너리 파일이 환경마다 달라질 수 있습니다. 입문에서는 “블록 단위 복사” 감각만 잡으면 됩니다. 반드시 `"wb"`/`"rb"`를 씁니다.

아래를 실행해 `nums.bin`에 쓴 뒤 다시 읽어 출력해 보세요.

```c
#include <stdio.h>

int main(void)
{
    FILE *fp;
    int out_data[3] = {10, 20, 30};
    int in_data[3] = {0, 0, 0};
    int i;

    fp = fopen("nums.bin", "wb");
    if (fp == NULL)
    {
        return 1;
    }
    fwrite(out_data, sizeof(int), 3, fp);
    fclose(fp);

    fp = fopen("nums.bin", "rb");
    if (fp == NULL)
    {
        return 1;
    }
    fread(in_data, sizeof(int), 3, fp);
    fclose(fp);

    for (i = 0; i < 3; i++)
    {
        printf("%d ", in_data[i]);
    }
    printf("\n");
    return 0;
}
```

**예상 출력**

```text
10 20 30
```

**코드 해석**

- `"wb"`로 정수 3개를 바이너리로 씁니다.
- `"rb"`로 같은 크기만큼 읽어 `in_data`에 담습니다.
- 내용이 `10 20 30`으로 복원됩니다.

### 24-5 파일 위치 지시자

스트림에는 **현재 위치**가 있습니다. `ftell`은 그 위치를 바이트 오프셋으로 알려 줍니다. `fseek`는 위치를 옮깁니다. 기준은 파일 시작 `SEEK_SET`, 현재 `SEEK_CUR`, 끝 `SEEK_END`입니다. `rewind`는 시작으로 되돌립니다. `fseek(fp, 0, SEEK_SET);`과 같은 목적입니다.

위치를 옮긴 뒤 읽으면 중간부터 데이터를 가져올 수 있습니다. 쓰기 모드에서도 위치가 중요합니다.

| 함수 | 역할 |
|---|---|
| `ftell` | 현재 위치 |
| `fseek` | 위치 이동 |
| `rewind` | 맨 앞으로 |

아래를 실행해 두 번째 줄만 읽는지 확인해 보세요. 먼저 `lines.txt`를 만드는 코드도 함께 있습니다.

```c
#include <stdio.h>

int main(void)
{
    FILE *fp;
    char buf[64];

    fp = fopen("lines.txt", "w");
    if (fp == NULL)
    {
        return 1;
    }
    fprintf(fp, "first\nsecond\nthird\n");
    fclose(fp);

    fp = fopen("lines.txt", "r");
    if (fp == NULL)
    {
        return 1;
    }
    fgets(buf, sizeof(buf), fp);
    fgets(buf, sizeof(buf), fp);
    printf("%s", buf);
    rewind(fp);
    fgets(buf, sizeof(buf), fp);
    printf("%s", buf);
    fclose(fp);
    return 0;
}
```

**예상 출력**

```text
second
first
```

**코드 해석**

- 첫 `fgets`로 `first`를 읽고, 둘째로 `second`를 읽어 출력합니다.
- `rewind` 후 다시 읽으면 맨 앞 `first`입니다.
- 위치 지시자가 읽기 위치를 기억합니다.

### 연습문제

**문제 1**
- 문제: 이름 문자열과 점수 정수를 입력받아 `record.txt`에 한 줄로 저장하세요.
- 입력: 이름 1개, 점수 1개. 예: `Lee` `88`
- 출력: 콘솔에 `ok` 한 줄. 파일 내용 예: `Lee 88`
- 조건: `fopen` 실패 시 메시지 후 종료. 끝나면 `fclose`

**문제 2**
- 문제: `record.txt`를 열어 이름과 점수를 읽어 콘솔에 출력하세요.
- 입력: 문제 1에서 만든 파일
- 출력: `Lee 88` 형태
- 조건: `"r"` 모드. `fscanf` 또는 `fgets` 사용

**문제 3**
- 문제: 정수 배열 `{1, 2, 3, 4}`를 바이너리 파일 `arr.bin`에 쓴 뒤, 다시 읽어 합을 출력하세요.
- 입력: 없음
- 출력: `10`
- 조건: `fwrite`/`fread`. 모드 `"wb"`/`"rb"`

### 정답 포인트

- 문제 1: `fopen(..., "w")` → `NULL` 검사 → `fprintf` → `fclose` → `puts("ok");`
- 문제 2: `"r"`로 열어 읽은 값을 `printf`로 확인
- 문제 3: `sizeof(int)`와 개수 `4`로 쓰고 읽은 뒤 합산
- 공통: 열기 실패 처리, 사용 후 `fclose`, 텍스트와 바이너리 모드 구분

---

[상위 문서로 돌아가기](./README.md)
