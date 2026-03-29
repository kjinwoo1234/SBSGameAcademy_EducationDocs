# C 입출력

프로그램은 "입력(Input) -> 처리(Process) -> 출력(Output)" 흐름으로 동작합니다.  
C 언어에서는 표준 입력/출력 라이브러리 `stdio.h`를 통해 콘솔 입출력을 처리합니다.

## 학습 목표
- `printf`와 `scanf`의 기본 사용법을 설명할 수 있다.
- 형식 지정자와 변수 타입을 올바르게 매칭할 수 있다.
- 입력/출력 코드에서 자주 발생하는 실수를 점검할 수 있다.

## 핵심
- 출력: `printf`
- 입력: `scanf`
- 안전한 입력: 변수 주소(`&`) 전달, 포맷 일치 확인

## 1) 출력: `printf`
`printf`는 지정한 형식(format)에 맞춰 데이터를 화면에 출력합니다.

- `%d`: 정수(`int`)
- `%f`: 실수(`float`, `double`)
- `%c`: 문자(`char`)
- `%s`: 문자열(`char[]`)

```c
#include <stdio.h>

int main(void) {
    int age = 20;
    double height = 175.5;
    char grade = 'A';
    char name[] = "Hong";

    printf("이름: %s\n", name);
    printf("나이: %d\n", age);
    printf("키: %.1f\n", height);   // 소수점 1자리
    printf("등급: %c\n", grade);
    return 0;
}
```

## 2) 입력: `scanf`
`scanf`는 키보드 입력을 변수에 저장합니다.  
숫자/문자를 입력받을 때는 **변수의 주소**를 넘겨야 하므로 `&`를 사용합니다.

```c
#include <stdio.h>

int main(void) {
    int age;
    double weight;

    printf("나이와 몸무게를 입력하세요 (예: 20 65.5): ");
    scanf("%d %lf", &age, &weight);   // double은 %lf

    printf("입력 결과 -> age: %d, weight: %.1f\n", age, weight);
    return 0;
}
```

## 3) 자주 하는 실수
- `scanf("%d", age);`처럼 `&`를 빼먹는 실수
- `double` 입력에 `%f`를 쓰는 실수 (`scanf`에서는 `%lf`)
- `printf` 포맷과 변수 타입이 불일치하는 실수

## 단계별 실습
1. 문자열/정수/실수를 각각 `printf`로 출력한다.
2. `scanf`로 정수와 실수를 입력받아 다시 출력한다.
3. 입력 포맷을 일부러 틀려보고(예: `%d`/`%lf` 교차) 왜 문제인지 확인한다.

## 이해 점검 체크리스트
- `printf`와 `scanf`의 역할 차이를 설명할 수 있는가?
- `scanf`에서 `&`가 필요한 이유를 설명할 수 있는가?
- 형식 지정자를 데이터 타입에 맞게 사용할 수 있는가?

## 4) 연습 문제
1. 이름(`%s`)과 나이(`%d`)를 입력받아 한 줄로 출력하세요.
2. 두 정수를 입력받아 합을 출력하세요.
3. 실수 1개를 입력받아 소수점 둘째 자리까지 출력하세요.

## 셀프 퀴즈
1. `scanf`에서 `double` 값을 입력받을 때 사용하는 형식 지정자는?
2. `scanf("%d", age);`가 위험한 이유는?

## 정답
1. `%lf`
2. 변수 주소(`&age`)를 넘기지 않아 잘못된 메모리에 접근할 수 있기 때문

---

[상위 문서로 돌아가기](./README.md)
