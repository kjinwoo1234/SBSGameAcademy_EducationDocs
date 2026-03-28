# [C++ 자습 자료] 01. 입출력과 변수/자료형

C++의 기초 문법과 표준 라이브러리 사용법을 학습합니다.

---

## 1. 콘솔 출력
`std::cout`을 사용해 데이터를 출력합니다.

[예제 코드]
```cpp
#include <iostream>

int main() {
    std::cout << "이름: 홍길동" << std::endl;
    std::cout << "나이: " << 20 << '\n';
    return 0;
}
```

---

## 2. 변수와 자료형
- `int`, `double`, `char`, `bool`
- 문자열은 `std::string` 사용 권장

[예제 코드]
```cpp
int age = 20;
double height = 175.5;
char grade = 'A';
bool isStudent = true;
std::string name = "홍길동";
```

---

## 3. 사용자 입력
`std::cin`으로 입력을 받습니다.

[예제 코드]
```cpp
int score;
std::cin >> score;
std::cout << "입력 점수: " << score << '\n';
```
