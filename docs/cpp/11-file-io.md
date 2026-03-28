# C++ 파일 입출력

파일 입출력은 프로그램 데이터를 영구 저장하는 기능입니다.  
C++에서는 스트림 기반 클래스(`fstream`)를 사용해 직관적으로 처리합니다.

## 1) 파일 쓰기
```cpp
#include <fstream>

std::ofstream out("log.txt");
if (out.is_open()) {
    out << "line1\n";
    out << "line2\n";
}
out.close();
```

## 2) 파일 읽기
```cpp
#include <string>
std::ifstream in("log.txt");
std::string line;
while (std::getline(in, line)) {
    std::cout << line << '\n';
}
in.close();
```

## 3) 파일 열기 모드
- `std::ios::app`: 이어쓰기
- `std::ios::binary`: 바이너리 모드

## 4) 연습 문제
1. 이름/점수를 파일에 저장하고 다시 읽어 출력하세요.
2. 실행 로그를 `app` 모드로 누적 기록하세요.

---

[상위 문서로 돌아가기](./README.md)
