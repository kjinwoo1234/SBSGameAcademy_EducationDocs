# C++ 변수와 자료형

변수는 프로그램 상태를 저장하는 이름 붙은 메모리 공간입니다.  
C++은 기본형 + 사용자 정의형 + 표준 라이브러리 타입을 함께 사용합니다.

## 1) 기본 자료형
- `int`: 정수
- `double`: 실수
- `char`: 문자
- `bool`: 논리값

```cpp
int age = 20;
double height = 175.5;
char grade = 'A';
bool isStudent = true;
```

## 2) 문자열 자료형
C 스타일 문자열보다 `std::string` 사용을 권장합니다.

```cpp
#include <string>
std::string name = "Hong";
std::cout << name.length() << '\n';
```

## 3) 형 변환
정수 나눗셈은 소수점이 버려집니다.

```cpp
int a = 7, b = 2;
double wrong = a / b;               // 3.0
double correct = static_cast<double>(a) / b; // 3.5
```

## 4) 연습 문제
1. 정수 2개를 입력받아 정수 나눗셈/실수 나눗셈을 비교 출력하세요.
2. `string`, `int`, `double`를 조합해 학생 정보를 출력하세요.

---

[상위 문서로 돌아가기](./README.md)
