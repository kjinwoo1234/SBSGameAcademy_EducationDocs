# C++ 변수와 자료형

변수는 프로그램 상태를 저장하는 이름 붙은 메모리 공간입니다.  
C++은 기본형 + 사용자 정의형 + 표준 라이브러리 타입을 함께 사용합니다.

## 학습 목표
- C++ 주요 자료형과 `std::string` 활용을 설명할 수 있다.
- 형 변환을 안전하게 적용할 수 있다.
- 입력/연산 과정에서 자료형 관련 실수를 점검할 수 있다.

## 핵심
- 기본형: `int`, `double`, `char`, `bool`
- 문자열: `std::string` 권장
- 형 변환: `static_cast<double>(a)`

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

## 실수 포인트
- `int` 나눗셈 결과를 실수라고 오해한다.
- C 스타일 캐스팅을 남용해 의도를 흐린다.
- 문자열 입력 시 공백 처리 방식을 혼동한다.

## 단계별 실습
1. 기본 자료형 변수를 선언하고 값을 출력한다.
2. `std::string` 변수를 만들고 길이를 출력한다.
3. 정수/실수 나눗셈 결과를 각각 출력해 차이를 비교한다.

## 이해 점검 체크리스트
- [ ] `std::string`을 사용하는 이유를 설명할 수 있는가?
- [ ] `static_cast`를 사용한 형 변환 코드를 작성할 수 있는가?
- [ ] 자료형 차이로 생기는 계산 결과 차이를 설명할 수 있는가?

## 4) 연습 문제
1. 정수 2개를 입력받아 정수 나눗셈/실수 나눗셈을 비교 출력하세요.
2. `string`, `int`, `double`를 조합해 학생 정보를 출력하세요.

## 셀프 퀴즈
1. 정수 나눗셈 결과를 실수로 얻으려면 어떤 방식이 필요한가?
2. C++에서 문자열 처리에 C 스타일 배열보다 `std::string`을 권장하는 이유는?

## 정답
1. 피연산자 중 하나를 실수형으로 캐스팅(`static_cast<double>`)해야 한다.
2. 길이/연산 지원이 편하고 메모리 관리가 더 안전하기 때문

## 관련 브릿지 학습
- [디버깅 워크플로우 기초](../common/01-debugging-workflow.md)
- [버전 관리와 협업 기초](../common/02-version-control-collaboration.md)

---

[상위 문서로 돌아가기](./README.md)
