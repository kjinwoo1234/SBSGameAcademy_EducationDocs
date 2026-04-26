# Chapter 06 `printf` 함수와 `scanf` 함수 정리

## 학습 목표
- 콘솔 입출력의 핵심 함수 사용법을 익힌다.
- 형식 지정자와 입력 안정성의 기본을 이해한다.

## 세부 주제
### 06-1 `printf` 함수 이야기
- 형식 지정자, 폭/정밀도, 이스케이프 문자

### 06-2 `scanf` 함수 이야기
- 주소 연산자 `&`와 입력 형식 지정

## 실습 체크리스트
- 정수/실수/문자열을 입력받아 형식 맞춰 출력한다.

## 본문

### 06-1 `printf` 함수 이야기

`printf`는 값을 콘솔에 출력할 때 가장 많이 쓰는 함수입니다. `%d`, `%f`, `%c`, `%s` 같은 **형식 지정자**를 쓰면 데이터 타입에 맞게 변환해 보여 줄 수 있습니다. 출력 폭이나 소수 자릿수, 줄바꿈(`\n`)을 조합하면 결과를 표처럼 정돈하기도 쉬워집니다.

형식 지정자의 역할을 표로 정리하면 다음과 같습니다.

| 형식 지정자 | 대상 타입 | 설명 | 예시 |
|---|---|---|---|
| `%d` | `int` | 10진수 정수 형태로 출력 | `printf("%d", 25);` -> `25` |
| `%f` | `float`, `double` | 소수 형태로 출력(기본 소수점 6자리) | `printf("%f", 3.14);` |
| `%.자리수f` | `float`, `double` | 소수점 자리수 지정 | `printf("%.2f", 3.14159);` -> `3.14` |
| `%c` | `char` | 문자 1개 출력 | `printf("%c", 'A');` -> `A` |
| `%s` | `char[]`, `char*` | 문자열 출력(`\0` 전까지) | `printf("%s", "Kim");` |
| `%%` | 없음 | `%` 문자 자체 출력 | `printf("진행률 80%%");` -> `진행률 80%` |

`printf` 안에서 `%`는 “형식이 여기서 시작한다”는 특별한 기호입니다. 그래서 화면에 `%` 글자 자체를 찍고 싶을 때는 `%%`처럼 두 번 적습니다. 예를 들어 `printf("배터리: 95%%\n");`를 실행하면 화면에는 `배터리: 95%`가 출력됩니다.

형식 지정자 표 예시 코드는 다음과 같습니다.

```c
#include <stdio.h>

int main(void) {
    int n = 25;
    double pi = 3.14159;
    char ch = 'A';
    char text[] = "Kim";

    printf("%%d -> %d\n", n);
    printf("%%f -> %f\n", pi);
    printf("%%.2f -> %.2f\n", pi);
    printf("%%c -> %c\n", ch);
    printf("%%s -> %s\n", text);
    printf("%% -> %%\n");
    return 0;
}
```

**예상 출력**

```text
%d -> 25
%f -> 3.141590
%.2f -> 3.14
%c -> A
%s -> Kim
% -> %
```

출력 폭과 정렬을 한 줄에 묶어 쓰는 방식은 아래 표처럼 기억하면 됩니다.

| 형식 | 의미 |
|---|---|
| `%5d` | 최소 5칸 확보(오른쪽 정렬) |
| `%-5d` | 최소 5칸 확보(왼쪽 정렬) |
| `%8.2f` | 전체 폭 8칸, 소수점 2자리 |

```c
#include <stdio.h>

int main(void) {
    int x = 42;
    double y = 3.5;

    printf("|%5d|\n", x);
    printf("|%-5d|\n", x);
    printf("|%8.2f|\n", y);
    return 0;
}
```

**예상 출력**

```text
|   42|
|42   |
|    3.50|
```

이스케이프 문자는 문자열 안에서 특수 동작을 내는 약속된 글자입니다.

| 문자 | 의미 |
|---|---|
| `\n` | 줄바꿈 |
| `\t` | 탭 간격 |
| `\"` | 큰따옴표 자체 출력 |

```c
#include <stdio.h>

int main(void) {
    printf("첫 줄\n둘째 줄\n");
    printf("A\tB\tC\n");
    printf("\"C language\"\n");
    return 0;
}
```

**예상 출력**

```text
첫 줄
둘째 줄
A	B	C
"C language"
```

```c
#include <stdio.h>

int main(void) {
    int age = 20;
    double height = 173.56;
    char grade = 'A';
    char name[] = "Kim";

    printf("이름: %s\n", name);
    printf("나이: %d\n", age);
    printf("키: %.1f\n", height);
    printf("등급: %c\n", grade);
    return 0;
}
```

**예상 출력**

```text
이름: Kim
나이: 20
키: 173.6
등급: A
```

### 06-2 `scanf` 함수 이야기

`scanf`는 키보드에서 읽은 값을 변수에 넣는 함수입니다. 값을 “어디에 써 넣을지” 알려 주려면 보통 변수의 **주소**가 필요하므로 `&변수명` 형태가 자주 나옵니다. 입력 형식과 변수 타입이 어긋나면 잘못된 값이 들어가거나 오류로 이어질 수 있으므로, 형식 지정자를 정확히 맞추는 것이 중요합니다.

`scanf`에서 형식 지정자와 넘기는 값의 대응은 아래 표처럼 정리할 수 있습니다.

| 형식 지정자 | 입력 대상 | 전달 방식 |
|---|---|---|
| `%d` | `int` 변수 | `&num` |
| `%f` | `float` 변수 | `&height` |
| `%lf` | `double` 변수 | `&weight` |
| `%c` | `char` 변수 | `&ch` |
| `%s` | 문자열 배열 | `name` (`&` 사용하지 않음) |

`scanf` 매핑 표 예시 코드는 다음과 같습니다.

```c
#include <stdio.h>

int main(void) {
    int num;
    float height;
    double weight;
    char grade;
    char name[20];

    printf("입력 예시: 10 173.5 65.2 A Kim\n");
    if (scanf("%d %f %lf %c %19s", &num, &height, &weight, &grade, name) != 5) {
        printf("입력 오류\n");
        return 1;
    }

    printf("num=%d, height=%.1f, weight=%.1lf, grade=%c, name=%s\n",
           num, height, weight, grade, name);
    return 0;
}
```

**예상 출력(입력: `10 173.5 65.2 A Kim`)**

```text
num=10, height=173.5, weight=65.2, grade=A, name=Kim
```

헷갈리기 쉬운 규칙은 표로 다시 짚으면 다음과 같습니다.

| 항목 | 규칙 |
|---|---|
| 숫자/실수/문자 1개 입력 | 보통 `&변수명` 사용 |
| 문자열 배열 입력 | 배열 이름(`name`) 자체 전달 (`&` 없음) |
| 공백 포함 문자열 | `scanf("%s")`만으로는 부족할 수 있어 `fgets` 등을 고려 |

아래 예시는 `scanf`의 반환값으로 “몇 개를 제대로 읽었는지”를 확인하는 패턴을 보여 줍니다. `int age`, `float height`, `char name[20]`으로 입력을 받을 자리를 만들고, `if (scanf("%19s %d %f", name, &age, &height) != 3)`에서 한 줄에 문자열·정수·실수를 순서대로 읽습니다. `%19s`는 최대 19글자까지만 받아 `name[20]`의 마지막 한 칸을 널 문자(`\0`)에 남겨 두려는 뜻입니다. `age`와 `height`는 값이 들어갈 위치가 필요하므로 `&`를 붙이고, `name`은 배열 이름이 시작 위치처럼 동작하므로 `&` 없이 넘깁니다. `printf("키: %.1f\n", height);`는 실수를 소수 첫째 자리까지 보여 줍니다.

```c
#include <stdio.h>

int main(void) {
    int age;
    float height;
    char name[20];

    printf("이름 나이 키 입력: ");
    if (scanf("%19s %d %f", name, &age, &height) != 3) {
        printf("입력 형식이 올바르지 않습니다.\n");
        return 1;
    }

    printf("이름: %s\n", name);
    printf("나이: %d\n", age);
    printf("키: %.1f\n", height);
    return 0;
}
```

자주 하는 실수는 형식 지정자와 변수 타입을 어긋나게 쓰는 경우, 숫자 변수에 `&`를 빼먹는 경우, 문자열 입력에서 길이 제한 없이 `%s`만 쓰는 경우로 묶을 수 있습니다.

### 연습문제

**문제 1**
- 문제: 정수 두 개를 입력받아 합·차·곱을 출력하세요.
- 입력: 정수 2개
- 출력: 합, 차, 곱
- 조건(힌트): `scanf("%d %d", &a, &b);`로 입력받으세요.

**문제 2**
- 문제: 문자열 하나를 입력받아 `"입력값: 문자열"` 형식으로 출력하세요.
- 입력: 공백 없는 문자열 1개
- 출력: `입력값: <문자열>`
- 조건(힌트): 버퍼 크기가 20이면 `%19s`처럼 길이 제한을 걸어 입력하세요.

**문제 3**
- 문제: 소수점 둘째 자리까지 출력하도록 `printf` 형식을 수정해 보세요.
- 입력: 실수 값 1개(예: `3.14159`)
- 출력: 소수점 둘째 자리까지 반올림된 값
- 조건(힌트): `printf("%.2f\n", value);`를 사용하세요.

### 정답 포인트

정수 입력은 `scanf("%d %d", &a, &b);`, 문자열은 `scanf("%19s", text);`, 소수 둘째 자리 출력은 `printf("%.2f", value);`처럼 맞추면 됩니다.

### 정답 예시

```c
#include <stdio.h>

int main(void) {
    int a, b;
    char text[20];
    double value = 3.14159;

    scanf("%d %d", &a, &b);
    printf("합: %d, 차: %d, 곱: %d\n", a + b, a - b, a * b);

    scanf("%19s", text);
    printf("입력값: %s\n", text);

    printf("%.2f\n", value);
    return 0;
}
```

---

[상위 문서로 돌아가기](./README.md)
