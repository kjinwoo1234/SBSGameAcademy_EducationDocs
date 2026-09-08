# Chapter 17 템플릿과 제네릭 프로그래밍

## 학습 목표

- 함수 템플릿으로 타입만 다른 중복을 줄일 수 있다.
- 클래스 템플릿으로 저장 타입을 바꿔 재사용할 수 있다.
- 템플릿이 요구하는 연산이 타입에 있는지 확인하는 습관을 익힌다.

## 본문

### 17-1 함수 템플릿

`int`용 최댓값 함수와 `double`용 최댓값 함수를 각각 만들면 본문이 거의 같습니다. **함수 템플릿**은 타입 자리만 비워 두고, 호출할 때 인자 타입을 보고 컴파일러가 실제 함수를 만들어 줍니다. 선언은 `template <typename T>` 다음에 함수를 적습니다. `T`는 “나중에 채워질 타입 이름”입니다.

```cpp
template <typename T>
T MaxValue(T a, T b)
{
    return (a > b) ? a : b;
}
```

`MaxValue(3, 5)`는 `T`가 `int`인 버전이, `MaxValue(2.5, 1.8)`는 `double` 버전이 됩니다. 같은 비교 로직을 타입마다 복사하지 않아도 됩니다. 다만 `T`가 `>` 연산을 지원해야 합니다. 지원하지 않는 타입으로 호출하면 오류 메시지가 길어질 수 있으니, **첫 번째 핵심 오류**부터 읽습니다.

| 용어 | 의미 |
|---|---|
| 템플릿 | 타입을 일반화해 재사용 코드를 만드는 문법 |
| 제네릭 | 특정 타입에 고정되지 않은 일반화 프로그래밍 방식 |

아래를 실행해 정수·실수 모두 최댓값이 나오는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

template <typename T>
T MaxValue(T a, T b)
{
    return (a > b) ? a : b;
}

int main()
{
    cout << MaxValue(3, 5) << endl;
    cout << MaxValue(2.5, 1.8) << endl;
    return 0;
}
```

**예상 출력**

```text
5
2.5
```

**코드 해석**

- `template <typename T>`는 타입 매개변수 `T`를 쓰겠다는 선언입니다.
- 호출 인자에 맞춰 `int`·`double`용 함수가 컴파일 시 만들어집니다.
- 비교에 `>`를 쓰므로, `T`는 서로 비교 가능한 타입이어야 합니다.

### 17-2 클래스 템플릿

**클래스 템플릿**은 멤버의 타입을 바깥에서 고르게 합니다. `Box<int>`는 정수를 담는 상자이고, `Box<string>`은 문자열을 담는 상자입니다. 클래스 앞에 같은 `template <typename T>`를 두고, 멤버와 멤버 함수에서 `T`를 씁니다.

객체를 만들 때는 `Box<int> bi(10);`처럼 **타입 인자를 명시**합니다. 함수 템플릿은 인자로 타입을 짐작하는 경우가 많지만, 클래스 템플릿은 보통 이름 옆에 `<...>`를 적습니다.

타입이 두 개면 `template <typename T1, typename T2>`처럼 매개변수를 늘립니다. `PairBox<string, int>`처럼 이름과 점수를 한 객체에 담을 때 유용합니다.

아래를 실행해 `int` 상자와 `string` 상자가 같은 설계로 동작하는지 확인해 보세요.

```cpp
#include <iostream>
#include <string>
using namespace std;

template <typename T>
class Box
{
public:
    Box(T value) : value(value)
    {
    }

    T Get() const
    {
        return value;
    }

    void Print() const
    {
        cout << value << endl;
    }

private:
    T value;
};

int main()
{
    Box<int> bi(10);
    Box<string> bs("검");
    bi.Print();
    bs.Print();
    return 0;
}
```

**예상 출력**

```text
10
검
```

**코드 해석**

- `Box<int>`는 `T`가 `int`인 클래스 버전입니다.
- `Box<string>`은 같은 멤버 구조로 문자열을 담습니다.
- `Print`는 `cout << value`가 가능한 타입에서 동작합니다.

### 17-3 사용할 때 주의점

템플릿 오류는 “어느 인스턴스에서 어떤 연산이 실패했는지”가 길게 이어질 수 있습니다. 메시지를 위에서부터 읽고, **호출한 줄**과 **요구 연산**을 먼저 확인합니다. `MaxValue`에 비교 불가능한 타입을 넣었는지, `Print`에 `<<`가 없는 타입을 넣었는지가 대표 원인입니다.

헤더에 템플릿을 둘 때는 선언과 정의를 한곳에 두는 경우가 많습니다. 지금은 한 `.cpp` 안에서 템플릿을 정의하고 `main`에서 쓰는 형태면 충분합니다. 복잡한 특수화·개념 검사는 이 장 범위 밖입니다.

이어서 두 타입을 담는 작은 예를 실행해 보세요.

```cpp
#include <iostream>
#include <string>
using namespace std;

template <typename T1, typename T2>
class PairBox
{
public:
    PairBox(T1 a, T2 b) : first(a), second(b)
    {
    }

    void Print() const
    {
        cout << first << " " << second << endl;
    }

private:
    T1 first;
    T2 second;
};

int main()
{
    PairBox<string, int> item("포션", 3);
    item.Print();
    return 0;
}
```

**예상 출력**

```text
포션 3
```

**코드 해석**

- `T1`은 `string`, `T2`는 `int`로 정해집니다.
- 한 클래스로 서로 다른 타입 쌍을 표현합니다.
- 출력에 쓰는 타입은 각각 `<<`가 가능해야 합니다.

이 장에서는 함수·클래스 템플릿의 기본만 다룹니다. 표준 라이브러리 컨테이너가 이런 템플릿으로 만들어져 있다는 감각을 가지고, 다음 장에서 STL 컨테이너를 더 깊게 봅니다.

### 연습문제

**문제 1**

- 문제: `MinValue` 함수 템플릿을 작성하세요.
- 입력: 없음. `MinValue(4, 2)`, `MinValue(9.5, 9.1)`처럼 호출해도 됩니다.
- 출력:
  ```text
  2
  9.1
  ```
- 조건: `template` 사용. 삼항 연산자 또는 `if`로 작은 값 반환

**문제 2**

- 문제: 값 하나를 저장·출력하는 클래스 템플릿 `Holder`를 작성하세요.
- 입력: 없음. `Holder<int>`, `Holder<string>` 각각 하나
- 출력: 저장한 값을 한 줄씩
- 조건: `Get` 또는 `Print` 멤버 제공

**문제 3**

- 문제: `PairBox`처럼 타입 두 개를 받아 한 줄에 출력하는 템플릿 클래스를 작성하세요.
- 입력: 없음. 예 `("엘릭서", 1)`
- 출력: `엘릭서 1`
- 조건: `template <typename T1, typename T2>`

### 정답 포인트

- 문제 1: `return (a < b) ? a : b;` 형태
- 문제 2: `template <typename T> class Holder` + 멤버 `T value`
- 문제 3: 생성자로 두 값 저장 후 `cout << first << " " << second`
- 공통: 타입만 다른 중복은 템플릿으로. 오류는 연산 가능 여부부터 점검

---

[상위 문서로 돌아가기](./README.md)
