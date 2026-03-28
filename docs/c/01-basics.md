# [C 자습 자료] 01. 입출력과 변수/자료형

C 언어의 가장 기초인 출력, 입력, 변수와 자료형을 학습합니다.

---

## 1. 콘솔 출력
`printf()`로 화면에 데이터를 출력합니다.

[예제 코드]
```c
#include <stdio.h>

int main() {
    printf("이름: %s\n", "홍길동");
    printf("나이: %d\n", 20);
    return 0;
}
```

---

## 2. 변수와 자료형
- `int`: 정수
- `float`, `double`: 실수
- `char`: 문자 1개

[예제 코드]
```c
int age = 20;
double height = 175.5;
char grade = 'A';
```

---

## 3. 사용자 입력
`scanf()`로 입력을 받아 변수에 저장합니다.

[예제 코드]
```c
int age;
scanf("%d", &age);
printf("입력한 나이: %d\n", age);
```

---

## 4. 형 변환
정수 연산과 실수 연산의 차이를 구분해야 합니다.

[예제 코드]
```c
int a = 7, b = 2;
double result1 = a / b;         // 3.0
double result2 = (double)a / b; // 3.5
```
