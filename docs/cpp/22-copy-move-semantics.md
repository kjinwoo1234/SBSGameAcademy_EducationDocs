# Chapter 22 복사/이동 시맨틱

## 학습 목표

- 복사와 이동이 자원을 어떻게 다루는지 구분할 수 있다.
- 깊은 복사와 `std::move` 이동으로 이중 해제를 피할 수 있다.
- Rule of 3/5로 자원 소유 클래스의 특수 멤버를 설계할 수 있다.

## 본문

### 22-1 복사 시맨틱

**시맨틱**은 코드가 어떤 의미로 동작하는지에 대한 약속입니다. **복사**는 같은 값을 가진 **새 객체**를 만드는 일입니다. `int`처럼 값만 있으면 단순하지만, 클래스 안에 **포인터로 가리킨 힙 메모리**가 있으면 이야기가 달라집니다.

컴파일러가 만들어 주는 기본 복사는 멤버를 그대로 복사합니다. 포인터 멤버는 **주소만** 복사되므로, 두 객체가 같은 메모리를 가리키게 됩니다. 이것을 **얕은 복사**라고 합니다. 둘 다 소멸자에서 `delete`하면 **이중 해제**로 프로그램이 깨질 수 있습니다. 새 메모리를 확보해 내용까지 복사하는 방식을 **깊은 복사**라고 합니다.

| 용어 | 의미 |
|---|---|
| 얕은 복사 | 포인터 주소만 복사해 같은 자원을 공유 |
| 깊은 복사 | 새 자원을 확보해 내용까지 복사 |
| 이중 해제 | 같은 주소를 두 번 `delete`하는 오류 |

아래를 실행해 복사 생성자가 호출되고, 복사본을 바꿔도 원본 값이 유지되는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Buffer
{
public:
    explicit Buffer(int value) : data(new int(value))
    {
    }

    ~Buffer()
    {
        delete data;
    }

    Buffer(const Buffer& other) : data(new int(*other.data))
    {
        cout << "copy ctor" << endl;
    }

    int Get() const { return *data; }
    void Set(int value) { *data = value; }

private:
    int* data;
};

int main()
{
    Buffer a(10);
    Buffer b = a;
    b.Set(20);
    cout << "a=" << a.Get() << " b=" << b.Get() << endl;
    return 0;
}
```

**예상 출력**

```text
copy ctor
a=10 b=20
```

**코드 해석**

- `Buffer b = a;`에서 복사 생성자가 새 `int`를 할당합니다.
- `b.Set(20)`은 복사본만 바꿉니다. `a`는 `10`을 유지합니다.
- 소멸자가 각자 `delete`해도 주소가 다르므로 이중 해제가 나지 않습니다.

### 22-2 이동 시맨틱

**이동**은 내용을 복제하지 않고 **소유권만 넘기는** 방식입니다. 임시 객체나 더 이상 쓰지 않을 객체의 자원을 옮길 때 비용을 줄입니다. **`std::move`**는 값을 물리적으로 옮기는 함수가 아니라, “이 객체를 **이동 가능**한 상태로 취급해도 된다”는 표시입니다. 헤더 `<utility>`가 필요합니다.

이동 생성자는 보통 포인터를 받아 온 뒤, 원본 포인터를 `nullptr`로 비웁니다. 원본 소멸자가 `delete`해도 빈 주소만 지우므로 안전합니다. 이동 후 원본은 **유효하지만 비어 있는 상태**로 두는 것이 규칙입니다.

| 항목 | 복사 | 이동 |
|---|---|---|
| 자원 | 새 자원 복제 | 소유권 이전 |
| 비용 | 상대적으로 큼 | 상대적으로 작음 |
| 원본 | 내용 유지 | 비어 있을 수 있음 |

아래를 실행해 복사 다음 이동 로그가 나오는지 확인해 보세요.

```cpp
#include <iostream>
#include <utility>
using namespace std;

class Buffer
{
public:
    explicit Buffer(int value) : data(new int(value))
    {
    }

    ~Buffer()
    {
        delete data;
    }

    Buffer(const Buffer& other) : data(new int(*other.data))
    {
        cout << "copy ctor" << endl;
    }

    Buffer(Buffer&& other) noexcept : data(other.data)
    {
        other.data = nullptr;
        cout << "move ctor" << endl;
    }

    int Get() const { return data ? *data : -1; }

private:
    int* data;
};

int main()
{
    Buffer a(10);
    Buffer b = a;
    Buffer c = std::move(a);
    cout << "c=" << c.Get() << endl;
    return 0;
}
```

**예상 출력**

```text
copy ctor
move ctor
c=10
```

**코드 해석**

- `Buffer b = a;`는 복사 생성자를 탑니다.
- `std::move(a)`는 이동 생성자를 고르게 합니다. `c`가 포인터를 가져가고 `a.data`는 `nullptr`이 됩니다.
- `noexcept`는 이동이 예외를 던지지 않는다고 알려, 컨테이너가 이동을 선호하게 만드는 힌트입니다.

### 22-3 Rule of 3/5

직접 `new`/`delete`로 자원을 소유하는 클래스는 특수 멤버를 함께 설계해야 합니다.

**Rule of 3**는 다음 셋이 필요하면 셋 다 직접 작성하라는 규칙입니다.

1. 소멸자
2. 복사 생성자
3. 복사 대입 연산자

이동까지 지원하면 **Rule of 5**로 확장합니다.

4. 이동 생성자
5. 이동 대입 연산자

복사·이동이 필요 없고 소유만 유일하게 두려면, 복사를 `= delete`로 막고 이동만 두는 설계도 있습니다. 입문에서는 “자원을 소유하면 소멸·복사·이동을 **빈칸으로 두지 않는다**”고 기억하면 됩니다.

아래는 복사 대입까지 포함한 짧은 예입니다. 실행해 `copy assign` 로그와 최종 값을 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Buffer
{
public:
    explicit Buffer(int value) : data(new int(value))
    {
    }

    ~Buffer()
    {
        delete data;
    }

    Buffer(const Buffer& other) : data(new int(*other.data))
    {
        cout << "copy ctor" << endl;
    }

    Buffer& operator=(const Buffer& other)
    {
        if (this == &other)
        {
            return *this;
        }
        delete data;
        data = new int(*other.data);
        cout << "copy assign" << endl;
        return *this;
    }

    int Get() const { return *data; }

private:
    int* data;
};

int main()
{
    Buffer a(1);
    Buffer b(2);
    b = a;
    cout << "b=" << b.Get() << endl;
    return 0;
}
```

**예상 출력**

```text
copy assign
b=1
```

**코드 해석**

- `operator=`는 자기 대입인지 먼저 검사합니다.
- 기존 메모리를 `delete`한 뒤 새 메모리를 할당해 깊은 복사합니다.
- 소멸자와 복사 생성자·대입이 한 세트로 맞춰져 있어야 자원이 안전합니다.

이 장에서는 복사·이동·Rule of 3/5까지입니다. `const`로 API를 다듬는 일은 다음 장에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: 문자열을 `new char[]`로 보관하는 클래스를 만들고, 복사 생성자에서 깊은 복사를 구현하세요.
- 입력: 없음. 생성자에 초기 문자열 리터럴을 넣어도 됨
- 출력: 복사 후 원본과 복사본 내용을 각각 한 줄씩. 복사본만 바꿔도 원본이 유지되면 통과
- 조건: 포인터만 복사하는 얕은 복사 금지. 소멸자에서 `delete[]`

**문제 2**

- 문제: 같은 클래스에 이동 생성자를 추가하고 `std::move`로 옮기세요.
- 입력: 없음
- 출력: `copy ctor` 또는 `move ctor` 같은 로그와, 이동 대상이 가진 값
- 조건: 이동 후 원본 포인터를 `nullptr`로 비우기. 가능하면 `noexcept`

**문제 3**

- 문제: 복사 대입 `operator=`를 구현하고 `b = a;`가 깊은 복사가 되게 하세요.
- 입력: 없음
- 출력: 대입 후 `b`의 값이 `a`와 같음
- 조건: 자기 대입 검사 포함. 기존 자원 해제 후 새 할당

### 정답 포인트

- 문제 1: 길이만큼 `new char[]` 후 문자 복사. 소멸자 `delete[]`
- 문제 2: 포인터 탈취 + 원본 `nullptr`. `std::move`로 이동 생성자 호출
- 문제 3: `this == &other` 가드 후 `delete` → 새 할당 → 내용 복사
- 공통: 자원 소유 클래스는 소멸·복사·이동을 세트로. 이동 후 원본은 안전한 빈 상태

---

[상위 문서로 돌아가기](./README.md)
