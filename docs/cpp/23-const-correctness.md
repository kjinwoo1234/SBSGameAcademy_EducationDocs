# Chapter 23 const 정확성과 API 설계

## 학습 목표

- 변수·포인터·참조에서 `const`가 무엇을 고정하는지 구분할 수 있다.
- `const` 멤버 함수로 조회 API와 수정 API를 나눌 수 있다.
- 읽기 전용 객체에서도 안전한 조회만 허용하는 설계를 적용할 수 있다.

## 본문

### 23-1 `const` 기본

**`const`**는 “이 이름은 수정하지 않겠다”는 약속입니다. 변수에 붙이면 대입이 막히고, 함수 매개변수에 붙이면 호출부가 넘긴 값을 함수가 바꾸지 않겠다는 뜻이 됩니다. 문법을 외우는 것보다, **누가 무엇을 못 바꾸게 할지**를 타입에 적는 도구로 쓰는 편이 중요합니다.

**API**는 Application Programming Interface의 약자입니다. 여기서는 클래스가 바깥에 공개한 함수 사용 규칙을 말합니다. 조회는 `const`로, 수정은 비-`const`로 나누면 호출자가 실수하기 어렵습니다.

| 용어 | 의미 |
|---|---|
| const 정확성 | 수정 가능·불가능 경로를 타입으로 구분하는 설계 |
| 읽기 전용 인터페이스 | 조회만 허용하는 함수 집합 |

아래를 실행해 `const int` 값을 읽고 출력하는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

int main()
{
    const int maxHp = 100;
    cout << maxHp << endl;
    // maxHp = 90;  // 컴파일 오류
    return 0;
}
```

**예상 출력**

```text
100
```

**코드 해석**

- `maxHp`는 선언 이후 값을 바꿀 수 없습니다.
- 주석 처리한 대입을 켜면 컴파일러가 수정을 거부합니다.
- 상수 이름에 의미를 두면 매직 넘버보다 읽기 쉽습니다.

### 23-2 포인터·참조와 `const`

`const`가 **어디에 붙느냐**에 따라 금지 대상이 달라집니다.

| 표현 | 의미 |
|---|---|
| `const int* p` | 가리키는 `int` 수정 금지. 포인터가 다른 주소를 가리키게 하는 것은 가능 |
| `int* const p` | 포인터가 가리키는 주소 고정. 그 주소의 값 수정은 가능 |
| `const int& r` | 복사 없이 원본을 읽기만 하는 참조 |

함수에 큰 `vector`를 넘길 때 `const vector<int>&`를 쓰면, 복사 비용을 피하면서 함수 안에서 내용을 바꾸지 못 하게 막을 수 있습니다. 11~18장에서 쓴 참조 전달에 **읽기 전용** 약속을 더한 형태입니다.

아래를 실행해 합계가 출력되고, 원본 벡터가 그대로인지 확인해 보세요.

```cpp
#include <iostream>
#include <vector>
using namespace std;

int Sum(const vector<int>& values)
{
    int total = 0;
    for (int v : values)
    {
        total += v;
    }
    return total;
}

int main()
{
    vector<int> nums = {1, 2, 3};
    cout << Sum(nums) << endl;
    cout << nums.size() << endl;
    return 0;
}
```

**예상 출력**

```text
6
3
```

**코드 해석**

- `const vector<int>&`는 복사하지 않고 읽기만 합니다.
- `Sum` 안에서 `values.push_back` 같은 수정은 컴파일되지 않습니다.
- `main`의 `nums`는 함수 호출 후에도 원소 개수 `3`을 유지합니다.

### 23-3 `const` 멤버 함수와 API 분리

멤버 함수 선언 끝의 **`const`**는 “이 함수는 객체의 논리 상태를 바꾸지 않는다”는 뜻입니다. **`const` 객체**나 `const` 참조로 받은 객체에서는 `const` 멤버만 호출할 수 있습니다. 체력을 깎는 `Damage`는 비-`const`, 체력을 읽는 `GetHp`는 `const`로 두는 식이 기본입니다.

| 종류 | 예 | `const` 객체에서 |
|---|---|---|
| 조회 | `int GetHp() const` | 호출 가능 |
| 수정 | `void Damage(int)` | 호출 불가 |

아래를 실행해 `const Player`에서도 체력을 읽을 수 있는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

class Player
{
public:
    explicit Player(int hp) : hp(hp)
    {
    }

    int GetHp() const
    {
        return hp;
    }

    void Damage(int amount)
    {
        hp -= amount;
        if (hp < 0)
        {
            hp = 0;
        }
    }

private:
    int hp;
};

void PrintHp(const Player& player)
{
    cout << player.GetHp() << endl;
    // player.Damage(1);  // 컴파일 오류
}

int main()
{
    Player hero(100);
    hero.Damage(20);
    PrintHp(hero);

    const Player guard(50);
    cout << guard.GetHp() << endl;
    return 0;
}
```

**예상 출력**

```text
80
50
```

**코드 해석**

- `GetHp() const`는 멤버를 바꾸지 않으므로 `const Player`와 `const Player&`에서 호출됩니다.
- `PrintHp`는 조회만 하므로 매개변수를 `const Player&`로 받습니다.
- `Damage`는 상태를 바꾸므로 `const` 객체에서는 호출할 수 없습니다.

이 장에서는 `const`로 조회·수정 경계를 긋는 것까지입니다. 이름 충돌과 헤더 분리는 다음 장에서 다룹니다.

### 연습문제

**문제 1**

- 문제: `vector<int>`를 `const vector<int>&`로 받아 합계를 반환하는 함수를 작성하세요.
- 입력: 없음. 본문처럼 `{1, 2, 3}`을 만들어 호출해도 됨
- 출력:
  ```text
  6
  ```
- 조건: 함수 안에서 벡터를 수정하는 코드 없음

**문제 2**

- 문제: `Inventory` 클래스에 아이템 추가와 개수 조회를 분리하세요.
- 입력: 없음. 코드에서 아이템을 2개 이상 추가
- 출력: 개수 숫자 한 줄. 예 `2`
- 조건: 조회 함수는 `const` 멤버. 추가는 비-`const`

**문제 3**

- 문제: `const Player` 객체를 만들고 체력만 출력하세요.
- 입력: 없음
- 출력: 생성 시 넣은 체력 값
- 조건: `GetHp() const` 사용. `const` 객체에서 수정 함수 호출 금지

### 정답 포인트

- 문제 1: `const vector<int>&` + 범위 `for`로 합산
- 문제 2: `Add`는 비-`const`, `Count() const`는 `items.size()` 반환
- 문제 3: `const Player p(값);` 후 `p.GetHp()`만 호출
- 공통: `const`는 설계 의도. 읽기 경로를 넓히면 API 실수가 줄어듦

---

[상위 문서로 돌아가기](./README.md)
