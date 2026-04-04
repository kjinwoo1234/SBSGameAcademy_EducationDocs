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
- `printf`는 값을 화면에 출력할 때 사용하는 대표 함수입니다.
- `%d`, `%f`, `%c`, `%s` 같은 형식 지정자를 이용하면 데이터 타입에 맞게 출력할 수 있습니다.
- 출력 폭, 소수점 자리수, 줄바꿈(`\n`)을 조합하면 결과를 보기 좋게 정리할 수 있습니다.

형식 지정자 설명:
| 형식 지정자 | 대상 타입 | 설명 | 예시 |
|---|---|---|---|
| `%d` | `int` | 10진수 정수 형태로 출력 | `printf("%d", 25);` -> `25` |
| `%f` | `float`, `double` | 소수 형태로 출력(기본 소수점 6자리) | `printf("%f", 3.14);` |
| `%.자리수f` | `float`, `double` | 소수점 자리수 지정 | `printf("%.2f", 3.14159);` -> `3.14` |
| `%c` | `char` | 문자 1개 출력 | `printf("%c", 'A');` -> `A` |
| `%s` | `char[]`, `char*` | 문자열 출력(`\0` 전까지) | `printf("%s", "Kim");` |
| `%%` | 없음 | `%` 문자 자체 출력 | `printf("진행률 80%%");` -> `진행률 80%` |

`%%`를 쓰는 이유:
- `printf`에서 `%`는 "형식 지정자가 시작된다"는 특별한 기호입니다.
- 그래서 `%` 문자 자체를 그대로 출력하고 싶으면 `%%`처럼 두 번 써야 합니다.
- 예: `printf("배터리: 95%%\n");` -> 화면에는 `배터리: 95%`가 출력됩니다.

형식 지정자 표 예시 코드:
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

예상 출력:
```text
%d -> 25
%f -> 3.141590
%.2f -> 3.14
%c -> A
%s -> Kim
% -> %
```

출력 폭/정렬 기초:
| 형식 | 의미 |
|---|---|
| `%5d` | 최소 5칸 확보(오른쪽 정렬) |
| `%-5d` | 최소 5칸 확보(왼쪽 정렬) |
| `%8.2f` | 전체 폭 8칸, 소수점 2자리 |

출력 폭/정렬 예시 코드:
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

예상 출력:
```text
|   42|
|42   |
|    3.50|
```

이스케이프 문자 기초:
| 문자 | 의미 |
|---|---|
| `\n` | 줄바꿈 |
| `\t` | 탭 간격 |
| `\"` | 큰따옴표 자체 출력 |

이스케이프 문자 예시 코드:
```c
#include <stdio.h>

int main(void) {
    printf("첫 줄\n둘째 줄\n");
    printf("A\tB\tC\n");
    printf("\"C language\"\n");
    return 0;
}
```

예상 출력:
```text
첫 줄
둘째 줄
A	B	C
"C language"
```

`printf` 예시:
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

예상 출력:
```text
이름: Kim
나이: 20
키: 173.6
등급: A
```

### 06-2 `scanf` 함수 이야기
- `scanf`는 키보드 입력을 받아 변수에 저장하는 함수입니다.
- 변수의 "주소"를 전달해야 하므로 보통 `&변수명` 형태를 사용합니다.
- 입력 형식과 변수 타입이 맞지 않으면 오류 또는 잘못된 값이 들어갈 수 있으니 형식 지정자를 정확히 맞춰야 합니다.

`scanf` 형식 지정자와 변수 연결:
| 형식 지정자 | 입력 대상 | 전달 방식 |
|---|---|---|
| `%d` | `int` 변수 | `&num` |
| `%f` | `float` 변수 | `&height` |
| `%lf` | `double` 변수 | `&weight` |
| `%c` | `char` 변수 | `&ch` |
| `%s` | 문자열 배열 | `name` (`&` 사용하지 않음) |

`scanf` 매핑 표 예시 코드:
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

예상 출력(입력: `10 173.5 65.2 A Kim`):
```text
num=10, height=173.5, weight=65.2, grade=A, name=Kim
```

`scanf`에서 자주 헷갈리는 규칙:
| 항목 | 규칙 |
|---|---|
| 숫자/실수/문자 1개 입력 | 보통 `&변수명` 사용 |
| 문자열 배열 입력 | 배열 이름(`name`) 자체 전달 (`&` 없음) |
| 공백 포함 문자열 | `scanf("%s")` 대신 `fgets` 고려 |

예시 코드:
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

코드 해석(줄 단위):
- `int age;`, `float height;`, `char name[20];`
  - 입력받을 값을 저장할 변수를 선언합니다.
- `if (scanf("%19s %d %f", name, &age, &height) != 3) { ... }`
  - 한 줄에서 문자열/정수/실수를 순서대로 입력받습니다.
  - `%19s`는 최대 19글자까지만 받아 버퍼 초과를 줄입니다. (`name[20]`의 마지막 1칸은 `\0`)
  - `age`, `height`는 값이 저장될 변수의 주소가 필요해서 `&`를 붙입니다.
  - `name`은 배열 이름 자체가 시작 주소처럼 동작하므로 `&` 없이 전달합니다.
  - `scanf`의 반환값으로 실제 입력 성공 개수를 확인하면 잘못된 입력을 더 안전하게 처리할 수 있습니다.
- `printf("키: %.1f\n", height);`
  - 실수를 소수점 첫째 자리까지 출력합니다.

자주 하는 실수:
- 형식 지정자와 변수 타입 불일치 (`%d` 자리에 `float` 등)
- `scanf`에서 숫자 변수에 `&` 누락
- 문자열 입력에서 길이 제한 없이 `%s`만 사용하는 실수

연습문제:
1. 정수 2개를 입력받아 합/차/곱을 출력하세요.
2. 문자열 1개를 입력받아 `"입력값: 문자열"` 형식으로 출력하세요.
3. 소수점 둘째 자리까지 출력하도록 `printf` 형식을 수정해보세요.

정답 포인트:
- 정수 입력: `scanf("%d %d", &a, &b);`
- 문자열 입력: `scanf("%19s", text);`
- 소수점 둘째 자리: `printf("%.2f", value);`

정답 예시:
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
