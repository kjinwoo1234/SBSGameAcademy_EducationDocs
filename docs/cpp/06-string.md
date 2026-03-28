# C++ 문자열

C++ 문자열은 `std::string`이 표준입니다.  
C 문자열보다 안전하고 편리한 연산을 기본 제공합니다.

## 1) 선언과 길이
```cpp
#include <string>

std::string name = "Kim";
std::cout << name.length() << '\n';
```

## 2) 문자열 결합/비교
```cpp
std::string first = "Hong";
std::string last = "GilDong";
std::string full = first + " " + last;

if (first == "Hong") {
    std::cout << "same\n";
}
```

## 3) 부분 문자열
```cpp
std::string s = "programming";
std::cout << s.substr(0, 7) << '\n'; // program
```

## 4) 연습 문제
1. 이름과 성을 입력받아 전체 이름을 출력하세요.
2. 입력 문자열 길이가 10 이상인지 검사하세요.

---

[상위 문서로 돌아가기](./README.md)
