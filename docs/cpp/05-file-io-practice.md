# [C++ 자습 자료] 05. 파일 입출력과 실습 가이드

## 1. 파일 입출력
`std::ofstream`, `std::ifstream`을 사용합니다.

```cpp
#include <fstream>
#include <string>

std::ofstream out("log.txt");
out << "Hello File\n";
out.close();

std::ifstream in("log.txt");
std::string line;
std::getline(in, line);
std::cout << line << '\n';
```

---

## 2. 실습 방향
1. 콘솔 계산기 만들기
2. 배열/함수 기반 성적 처리
3. 클래스 기반 캐릭터 상태 관리
4. 파일 저장/불러오기 기능 추가

---

## 체크 포인트
- 참조(`&`)를 활용한 함수 설계를 설명할 수 있는가?
- `new/delete`를 안전하게 사용하고 있는가?
- 구조체와 클래스의 차이를 코드로 설명할 수 있는가?
