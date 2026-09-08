# Chapter 10 동적 메모리와 스마트 포인터

## 학습 목표

- `new`/`delete`와 `new[]`/`delete[]`로 힙 메모리를 확보·해제할 수 있다.
- 메모리 누수와 소유권 개념을 설명할 수 있다.
- `unique_ptr`·`shared_ptr`로 기본 수명 관리를 할 수 있다.

## 본문

### 10-1 `new`와 `delete`

앞 장까지 쓰던 지역 변수는 함수가 끝나면 사라지는 **자동 수명**입니다. 실행 중에 크기를 정하거나, 함수가 끝난 뒤에도 값을 남겨 두고 싶을 때는 **동적 할당**을 씁니다. **`new`**는 **힙**에 공간을 만들고 그 **주소**를 돌려줍니다. `int* p = new int(42);`는 힙에 정수 하나를 두고 `42`로 초기화한 뒤, 주소를 `p`에 담습니다.

힙에 만든 메모리는 함수가 끝나도 자동으로 사라지지 않습니다. 다 쓰면 **`delete p;`**로 반납합니다. `new`로 만든 주소에는 `delete`를, 배열이면 다음 소절의 `delete[]`를 짝으로 맞춥니다. `delete` 뒤에는 그 포인터로 다시 읽지 않습니다.

아래를 실행해 힙에 만든 값을 읽고, 해제한 뒤에도 프로그램이 끝나는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

int main()
{
    int* p = new int(42);
    cout << *p << endl;
    delete p;
    p = nullptr;
    return 0;
}
```

**예상 출력**

```text
42
```

**코드 해석**

- `new int(42)`가 힙에 정수를 만들고 주소를 `p`에 줍니다.
- `*p`로 값을 읽어 출력합니다.
- `delete p`로 메모리를 반납하고, `p = nullptr`로 “더 이상 유효하지 않음”을 표시합니다.

### 10-2 동적 배열과 소유권

원소 개수를 **실행 중**에 정하려면 `new int[n]`처럼 **배열 형태**로 할당합니다. 해제는 **`delete[]`**입니다. `delete`와 `delete[]`를 섞으면 안 됩니다. 크기를 입력받아 배열을 채운 뒤 출력하고, 끝나면 `delete[]`로 반납하는 흐름을 익힙니다.

**메모리 누수**는 더 이상 쓸 수 없는 힙 메모리를 `delete`하지 않고 남겨 둔 상태입니다. 주소를 담던 포인터를 잃어버리면 해제할 길도 없습니다. **소유권**은 “이 메모리를 누가 언제 해제할 책임인가”입니다. 한 주소에 대해 `delete`를 두 번 호출하는 것도 위험합니다. 한 소유자가 한 번만 해제하도록 역할을 분명히 둡니다.

| 할당 | 해제 | 용도 |
|---|---|---|
| `new T` | `delete` | 객체·값 하나 |
| `new T[n]` | `delete[]` | 길이 `n` 배열 |

| 용어 | 의미 |
|---|---|
| 힙 | `new`로 확보하는 동적 메모리 영역 |
| 메모리 누수 | 해제되지 않고 남는 동적 메모리 |
| 소유권 | 생성·해제 책임을 지는 주체 |

아래를 실행해 크기를 입력한 뒤 값을 채우고 출력한 다음, `delete[]`로 정리하는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n = 0;
    cin >> n;

    int* arr = new int[n];
    for (int i = 0; i < n; ++i)
    {
        arr[i] = (i + 1) * 10;
    }

    for (int i = 0; i < n; ++i)
    {
        cout << arr[i] << ' ';
    }
    cout << endl;

    delete[] arr;
    arr = nullptr;
    return 0;
}
```

**입력 예**

```text
3
```

**예상 출력**

```text
10 20 30
```

**코드 해석**

- `new int[n]`으로 길이 `n` 배열을 힙에 만듭니다.
- 인덱스로 값을 넣고 출력합니다. 앞 장 배열과 쓰는 법은 같습니다.
- `delete[] arr`로 배열 전체를 반납합니다.

크기를 미리 알고 `vector`로 충분하면 `vector`가 더 단순합니다. 이 장은 “힙을 직접 다루는 규칙”을 익히는 단계입니다. 직접 `new`/`delete`를 쓸수록 소유권을 코드로 드러내는 습관이 필요합니다.

### 10-3 스마트 포인터

**스마트 포인터**는 포인터처럼 주소를 다루면서, 수명이 끝나면 **자동으로 해제**해 `delete` 누락을 줄입니다. `<memory>` 헤더의 **`unique_ptr`**는 **단독 소유**입니다. 복사로 소유자를 늘릴 수 없고, 필요하면 이동으로 소유권을 넘깁니다. **`shared_ptr`**는 **참조 횟수**로 여러 곳이 함께 소유하고, 마지막 소유자가 사라질 때 해제합니다.

입문에서는 생성을 `make_unique`·`make_shared`로 두는 편이 안전합니다. `unique_ptr<int> p = make_unique<int>(42);`처럼 쓰고, 값은 `*p`로 읽습니다. 범위가 끝나면 자동 해제되므로 `main` 끝에서 `delete`를 쓰지 않습니다.

| 타입 | 소유 | 복사 | 이럴 때 |
|---|---|---|---|
| `unique_ptr` | 단독 | 불가. 이동만 | 소유자가 하나일 때 |
| `shared_ptr` | 공동 | 가능 | 여러 곳이 같은 대상을 쓸 때 |

아래를 실행해 `unique_ptr`로 값을 출력해 보세요. `delete`를 직접 호출하지 않아도 됩니다.

```cpp
#include <iostream>
#include <memory>
using namespace std;

int main()
{
    unique_ptr<int> p = make_unique<int>(42);
    cout << *p << endl;
    return 0;
}
```

**예상 출력**

```text
42
```

**코드 해석**

- `make_unique<int>(42)`가 힙 정수를 만들고 소유권을 `p`에 줍니다.
- `*p`로 값을 읽습니다.
- `main`이 끝나면 `unique_ptr`가 자동으로 메모리를 해제합니다.

이어서 `shared_ptr`로 같은 값을 두 변수가 함께 가리키는 예입니다.

```cpp
#include <iostream>
#include <memory>
using namespace std;

int main()
{
    shared_ptr<int> a = make_shared<int>(7);
    shared_ptr<int> b = a;
    *b = 9;
    cout << *a << ' ' << *b << endl;
    return 0;
}
```

**예상 출력**

```text
9 9
```

**코드 해석**

- `b = a`로 같은 힙 정수를 함께 소유합니다.
- `*b = 9`로 바꾸면 `*a`도 `9`입니다.
- `a`와 `b`가 모두 사라질 때 한 번 해제됩니다.

새 코드에서는 가능하면 스마트 포인터를 기본으로 두고, 학습·기존 API 때문에 날포인터가 필요할 때만 `new`/`delete` 짝을 직접 맞춥니다.

### 연습문제

**문제 1**

- 문제: `new`로 정수 하나를 만들고 값을 출력한 뒤 해제하세요.
- 입력: 없음. 예로 값 `100`
- 출력: `100`
- 조건: `delete` 사용. 해제 후 포인터는 `nullptr`로 두기

**문제 2**

- 문제: `new`로 정수 배열을 만들고 값을 출력한 뒤 해제하세요.
- 입력: 배열 크기 1개. 예 `4`
- 출력: 원소들. 예 `1 2 3 4`처럼 채워도 됨
- 조건: `delete[]` 사용

**문제 3**

- 문제: 문제 1과 같은 결과를 `unique_ptr`로 작성하세요.
- 입력: 없음
- 출력: `100`
- 조건: `make_unique` 사용. 직접 `delete` 호출 금지

### 정답 포인트

- 문제 1: `int* p = new int(100); cout << *p; delete p; p = nullptr;`
- 문제 2: `int* arr = new int[n];` … `delete[] arr;`
- 문제 3: `unique_ptr<int> p = make_unique<int>(100); cout << *p;`
- 공통: `new`↔`delete`, `new[]`↔`delete[]`. 가능하면 `unique_ptr`부터

---

[상위 문서로 돌아가기](./README.md)
