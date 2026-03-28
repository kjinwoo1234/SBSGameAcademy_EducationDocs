# C++ 입출력

C++ 표준 입출력은 `iostream` 기반으로 동작합니다.  
연산자(`<<`, `>>`)를 통해 타입 안전성과 확장성이 높은 입출력을 제공합니다.

## 1) 출력: `std::cout`
- `<<` 연산자로 여러 값을 연속 출력
- 줄바꿈은 `'\n'` 또는 `std::endl`

```cpp
#include <iostream>

int main() {
    std::cout << "이름: Hong\n";
    std::cout << "나이: " << 20 << '\n';
    return 0;
}
```

## 2) 입력: `std::cin`
`>>` 연산자로 변수에 값을 입력받습니다.

```cpp
int age;
std::cout << "나이 입력: ";
std::cin >> age;
std::cout << "입력한 나이: " << age << '\n';
```

## 3) 공백 포함 문자열 입력
`cin >> str`은 공백 전까지만 읽습니다.  
한 줄 전체 입력은 `std::getline`을 사용합니다.

```cpp
#include <string>

std::string line;
std::getline(std::cin, line);
```

## 4) 연습 문제
1. 이름/나이를 입력받아 자기소개 문장을 출력하세요.
2. 정수 2개를 입력받아 합/평균을 출력하세요.

---

[상위 문서로 돌아가기](./README.md)
