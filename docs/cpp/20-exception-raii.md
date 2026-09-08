# Chapter 20 예외 처리와 RAII

## 학습 목표

- `try` / `catch` / `throw`로 오류 경로를 분리할 수 있다.
- `runtime_error` 등으로 원인을 메시지로 전달할 수 있다.
- RAII로 객체 수명에 자원 정리를 묶어 누수를 줄인다.

## 본문

### 20-1 예외 처리 기초

**예외**는 정상 흐름만으로는 다루기 어려운 오류를, 별도 경로로 알리는 방법입니다. 문제가 생긴 곳에서 **`throw`**로 예외 객체를 던지고, **`try`** 블록 안에서 실행하다 **`catch`**에서 받습니다. 헤더 `<stdexcept>`의 `runtime_error`는 실행 중 오류 메시지를 담을 때 자주 씁니다.

```cpp
try
{
    // 위험할 수 있는 작업
}
catch (const exception& e)
{
    cout << e.what() << endl;
}
```

`e.what()`은 예외에 담긴 설명 문자열을 돌려줍니다. 0으로 나누기처럼 “계산을 진행하면 안 되는 입력”을 함수 안에서 발견하면, 잘못된 값을 억지로 반환하기보다 예외로 알리는 편이 호출부 의도가 분명해질 수 있습니다.

| 용어 | 의미 |
|---|---|
| `throw` | 예외를 발생시킴 |
| `try` | 예외가 날 수 있는 코드를 감쌈 |
| `catch` | 던져진 예외를 받아 처리 |

아래를 실행해 제수가 0일 때 메시지가 출력되는지 확인해 보세요.

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

int Divide(int a, int b)
{
    if (b == 0)
    {
        throw runtime_error("0으로 나눌 수 없음");
    }
    return a / b;
}

int main()
{
    try
    {
        cout << Divide(10, 2) << endl;
        cout << Divide(10, 0) << endl;
    }
    catch (const exception& e)
    {
        cout << e.what() << endl;
    }
    return 0;
}
```

**예상 출력**

```text
5
0으로 나눌 수 없음
```

**코드 해석**

- `Divide(10, 2)`는 정상적으로 `5`를 출력합니다.
- `Divide(10, 0)`에서 `throw`가 발생해 다음 `cout`은 실행되지 않습니다.
- `catch`가 메시지를 받아 출력한 뒤 프로그램은 정상 종료합니다.

### 20-2 예외를 어디에 둘지

예외는 “여기서 정책을 정할 수 있는가?”를 기준으로 둡니다. 라이브러리성 함수는 잘못된 인자를 발견하면 던지고, `main`이나 UI에 가까운 곳에서 사용자 메시지를 정하는 식이 흔합니다. 모든 `if` 실패를 예외로 바꾸지는 않습니다. 예상 가능한 분기와, 복구 정책이 위로 가야 하는 오류를 구분합니다.

`catch`에서는 가능하면 `const exception&`처럼 **참조**로 받습니다. 잘린 객체 복사와 추가 할당을 줄이기 위한 습관입니다. 여러 종류를 구분해 처리해야 하면 `catch`를 여러 개 둘 수 있지만, 입문에서는 `exception` 하나로도 충분합니다.

파일 열기처럼 실패가 흔한 작업도 예외로 알릴 수 있습니다. 12장에서 쓴 `ifstream`이 열리지 않으면 `throw runtime_error("파일 열기 실패");`처럼 원인을 남깁니다.

아래를 실행해 없는 파일을 열 때 메시지가 나오는지 확인해 보세요. `not_found.txt`가 없어야 예외 경로를 봅니다.

```cpp
#include <fstream>
#include <iostream>
#include <stdexcept>
using namespace std;

int main()
{
    try
    {
        ifstream fin("not_found.txt");
        if (!fin)
        {
            throw runtime_error("파일 열기 실패");
        }
        cout << "open success" << endl;
    }
    catch (const exception& e)
    {
        cout << e.what() << endl;
    }
    return 0;
}
```

**예상 출력**

```text
파일 열기 실패
```

**코드 해석**

- `fin`이 열리지 않으면 `!fin`이 참입니다.
- `throw`로 원인을 던지고 `catch`에서 `what()`을 출력합니다.
- 성공 시만 `open success`가 나옵니다.

### 20-3 RAII

**RAII**는 Resource Acquisition Is Initialization의 줄임말입니다. **자원을 얻는 시점**을 **객체 초기화**에 묶고, **객체가 소멸할 때** 자원을 반납한다는 관용구입니다. 파일 스트림, 스마트 포인터가 대표 예입니다. `ifstream` 객체가 범위를 벗어나면 소멸자가 파일을 닫습니다. 10장의 `unique_ptr`도 범위가 끝나면 메모리를 해제합니다.

예외가 중간에 나더라도, 스택에 있던 지역 객체의 소멸자는 호출됩니다. 그래서 “성공 경로와 실패 경로마다 `delete`/`close`를 수동으로 짝짓기”보다, 자원을 객체에 맡기는 편이 누수 위험이 줄어듭니다. 직접 `new`한 메모리를 `try` 안에서만 `delete`하다가 `throw`로 빠져나가면 누수가 나기 쉽습니다. 그런 경우 `unique_ptr`로 소유권을 객체에 넘깁니다.

| 이름 조각 | 의미 |
|---|---|
| Resource | 메모리, 파일 핸들 같은 자원 |
| Acquisition | 자원을 얻음 |
| Is Initialization | 얻는 시점을 객체 생성·초기화와 같게 둠 |

아래를 실행해 `unique_ptr`가 예외 경로에서도 직접 `delete` 없이 끝나는 구조를 확인해 보세요. 출력은 예외 메시지입니다.

```cpp
#include <iostream>
#include <memory>
#include <stdexcept>
using namespace std;

void UseValue(bool fail)
{
    unique_ptr<int> p = make_unique<int>(42);
    cout << *p << endl;
    if (fail)
    {
        throw runtime_error("작업 실패");
    }
}

int main()
{
    try
    {
        UseValue(true);
    }
    catch (const exception& e)
    {
        cout << e.what() << endl;
    }
    return 0;
}
```

**예상 출력**

```text
42
작업 실패
```

**코드 해석**

- `unique_ptr`가 `42`를 소유한 뒤 값을 출력합니다.
- `throw`로 함수를 빠져나가도 `unique_ptr` 소멸자가 메모리를 해제합니다.
- `main`의 `catch`가 메시지만 처리하면 됩니다.

이 장에서는 예외 흐름과 RAII 감각이 핵심입니다. 복사·이동 시맨틱 같은 심화는 이후 장에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: 0으로 나누려 할 때 예외를 던지고 `catch`에서 메시지를 출력하세요.
- 입력: 없음. 피제수·제수는 코드에 두거나, 제수 `0`인 호출을 포함
- 출력: 오류 메시지 한 줄. 정상 나눗셈이 있으면 그 결과도 출력 가능
- 조건: `throw` 사용

**문제 2**

- 문제: 파일 경로를 받아 열기를 시도하고, 실패 시 예외를 던지는 함수를 작성하세요.
- 입력: 없음. 존재하지 않는 경로로 호출해 실패 경로를 확인
- 출력: `catch`에서 실패 메시지
- 조건: `runtime_error` 활용

**문제 3**

- 문제: `unique_ptr`로 정수를 만든 뒤, 함수 안에서 예외를 던져도 직접 `delete` 없이 끝나게 하세요.
- 입력: 없음
- 출력: 예외 메시지. 원하면 예외 전 값 출력
- 조건: `make_unique` 사용. 수동 `delete` 금지

### 정답 포인트

- 문제 1: `if (b == 0) throw runtime_error(...);` + `try`/`catch`
- 문제 2: `ifstream` 실패 시 `throw`, `main`에서 메시지 출력
- 문제 3: `unique_ptr` 지역 객체 + `throw` 후에도 소멸자가 해제
- 공통: 예외는 상위 정책이 필요할 때. RAII면 예외 경로 정리 누락이 줄어듦

---

[상위 문서로 돌아가기](./README.md)
