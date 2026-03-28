# C 문자열

C에서 문자열은 `char` 배열이며 끝에 널 문자(`\0`)가 자동으로 붙습니다.  
문자열 처리는 버퍼 크기와 종료 문자를 항상 의식해야 안전합니다.

## 1) 문자열 선언
```c
char name1[] = "Hong";              // {'H','o','n','g','\0'}
char name2[10] = "Kim";             // 남은 공간은 '\0'
char name3[] = {'L','e','e','\0'};  // 수동 초기화
```

## 2) 문자열 입출력
```c
char name[20];
scanf("%19s", name);          // 버퍼 초과 방지
printf("이름: %s\n", name);
```

## 3) 자주 쓰는 문자열 함수 (`string.h`)
- `strlen(s)`: 길이(널 문자 제외)
- `strcpy(dst, src)`: 복사
- `strcmp(a, b)`: 비교 (같으면 0)
- `strcat(dst, src)`: 이어붙이기

```c
#include <string.h>

char a[20] = "Hello";
char b[] = "World";
strcat(a, b);                 // HelloWorld
printf("%zu\n", strlen(a));   // 10
```

## 4) 문자열과 문자 배열 차이
- 문자열은 반드시 `\0` 종료 필요
- `char arr[3] = {'A','B','C'}`는 문자열이 아님

## 5) 연습 문제
1. 이름을 입력받아 길이를 출력하세요.
2. 두 문자열을 비교해 같으면 "동일"을 출력하세요.
3. 성과 이름 문자열을 합쳐 전체 이름을 만드세요.

---

[상위 문서로 돌아가기](./README.md)
