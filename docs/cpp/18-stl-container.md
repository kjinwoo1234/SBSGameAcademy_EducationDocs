# Chapter 18 STL 컨테이너 심화

## 학습 목표

- `vector` 외에 `deque`·`map`·`unordered_map`의 역할을 구분한다.
- 정렬이 필요한지·키로 빨리 찾을지에 따라 컨테이너를 고를 수 있다.
- 키-값 쌍을 넣고 조회하는 코드를 작성할 수 있다.

## 본문

### 18-1 순차 컨테이너 복습과 `deque`

**STL**은 Standard Template Library, 표준 템플릿 라이브러리입니다. 컨테이너·알고리즘·반복자처럼 자주 쓰는 부품을 모아 둡니다. 6장에서 배운 **`vector`**는 맨 뒤 추가와 인덱스 접근이 빠른 순차 컨테이너입니다. 대부분의 “목록” 요구에는 `vector`가 기본 선택입니다.

아래를 실행해 `vector`가 뒤에 붙이는 추가와 인덱스 접근에 어떻게 쓰이는지 확인해 보세요.

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    vector<int> v = {1, 2};
    v.push_back(3);

    cout << v[0] << endl;

    for (int x : v)
    {
        cout << x << " ";
    }
    cout << endl;
    return 0;
}
```

**예상 출력**

```text
1
1 2 3
```

**코드 해석**

- `push_back(3)`은 목록 **맨 뒤**에만 값을 붙입니다.
- `v[0]`처럼 인덱스로 원하는 칸을 바로 읽습니다.
- 범위 기반 `for`로 앞에서 뒤 순서대로 출력합니다.

`vector`에는 **맨 앞에 붙이는 전용 동작이 없습니다.** 앞자리에 넣으려면 뒤 원소를 한 칸씩 밀어야 해서 불리합니다.

**`deque`**는 양끝 삽입·삭제가 편한 순차 컨테이너입니다. 앞과 뒤 모두 `push_front` / `push_back`을 쓸 때 후보가 됩니다. 임의 위치 중간 삽입이 매우 잦으면 다른 구조를 검토하지만, 입문에서는 “양끝이 필요하면 `deque`, 아니면 `vector`” 정도로 기억하면 됩니다.

| 컨테이너 | 특징 | 이런 때 |
|---|---|---|
| `vector` | 뒤 추가·인덱스 접근에 강함 | 일반 목록, 점수 배열 |
| `deque` | 앞·뒤 추가가 편함 | 양끝 큐처럼 쓸 때 |

아래를 실행해 `deque` 앞뒤에 넣고 앞에서부터 출력되는지 확인해 보세요.

```cpp
#include <deque>
#include <iostream>
using namespace std;

int main()
{
    deque<int> dq;
    dq.push_back(2);
    dq.push_back(3);
    dq.push_front(1);

    for (int x : dq)
    {
        cout << x << " ";
    }
    cout << endl;
    return 0;
}
```

**예상 출력**

```text
1 2 3
```

**코드 해석**

- `push_back`으로 `2`, `3`을 뒤에 붙입니다.
- `push_front(1)`로 앞에 `1`을 넣습니다.
- 범위 기반 `for`로 앞부터 `1 2 3`이 나옵니다.

### 18-2 `map` — 정렬된 키-값

**연관 컨테이너**는 키로 값을 찾아 씁니다. **`map`**은 키를 **정렬된 상태**로 보관합니다. `map<string, int> scores;`처럼 쓰고, `scores["전사"] = 90;`으로 넣거나 바꿉니다. 같은 키로 읽으면 값이 나옵니다.

키 순서로 전체를 돌아볼 필요가 있으면 `map`이 유리합니다. 범위 기반 `for`로 돌릴 때는 `pair`처럼 `first`가 키, `second`가 값입니다. 헤더는 `<map>`입니다.

| 용어 | 의미 |
|---|---|
| 키 | 값을 찾기 위한 이름·식별자 |
| 값 | 키에 대응해 저장하는 데이터 |

아래를 실행해 키로 점수를 읽고, 전체를 순회해 보세요. `map`은 키 기준 정렬이라, 넣은 순서와 출력 순서가 다를 수 있습니다. 순회할 때 `pair`의 `first`가 키, `second`가 값입니다.

```cpp
#include <iostream>
#include <map>
#include <string>
using namespace std;

int main()
{
    map<string, int> scores;
    scores["전사"] = 90;
    scores["마법사"] = 85;
    scores["도적"] = 92;

    cout << scores["마법사"] << endl;

    for (pair<const string, int> entry : scores)
    {
        cout << entry.first << " " << entry.second << endl;
    }
    return 0;
}
```

**예상 출력**

```text
85
도적 92
마법사 85
전사 90
```

**코드 해석**

- `scores["마법사"]`로 키에 해당하는 값을 읽습니다.
- 순회 시 키 문자열 정렬 순으로 나옵니다.
- `entry.first`는 이름, `entry.second`는 점수입니다.

### 18-3 `unordered_map` — 빠른 조회

**`unordered_map`**도 키-값 쌍을 담지만, 내부를 **해시**로 관리합니다. 평균적으로 키 조회가 매우 빠르고, **정렬 순서는 보장하지 않습니다.** 인벤토리처럼 “이름만 알면 개수를 빨리 알고 싶다”는 요구에 잘 맞습니다. 헤더는 `<unordered_map>`입니다.

| 컨테이너 | 정렬 | 탐색 감각 | 적합한 상황 |
|---|---|---|---|
| `vector` | 없음 | 인덱스가 빠름 | 순차 데이터 |
| `map` | 키 정렬 | 로그 시간에 가깝게 탐색 | 정렬 순회가 필요할 때 |
| `unordered_map` | 없음 | 평균 매우 빠른 조회 | 키로 값만 빨리 찾을 때 |

선택 기준은 “정렬이 필요한가?”입니다. 필요하면 `map`, 아니면 조회 속도 중심으로 `unordered_map`을 고려합니다. 단순 목록이면 여전히 `vector`가 단순합니다.

아래를 실행해 아이템 개수를 키로 조회해 보세요.

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;

int main()
{
    unordered_map<string, int> itemCount;
    itemCount["potion"] = 3;
    itemCount["elixir"] = 1;

    cout << itemCount["potion"] << endl;

    itemCount["potion"] = itemCount["potion"] + 1;
    cout << itemCount["potion"] << endl;
    return 0;
}
```

**예상 출력**

```text
3
4
```

**코드 해석**

- `unordered_map<string, int>`는 문자열 키와 정수 값입니다.
- `itemCount["potion"] = 3`으로 삽입·갱신합니다.
- 같은 키로 읽어 더한 뒤 다시 저장해 개수를 늘립니다.

이 장에서는 컨테이너 선택과 키-값 사용이 핵심입니다. 정렬·조건 개수처럼 “데이터에 대한 처리”는 다음 장 알고리즘·람다에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: 이름-점수 매핑을 저장하고 특정 이름 점수를 출력하세요.
- 입력: 없음. 코드에 이름 3개·점수 3개를 넣어도 됩니다. 조회 이름은 그중 하나
- 출력: 조회한 점수 숫자 한 줄
- 조건: `map` 또는 `unordered_map` 사용

**문제 2**

- 문제: 문자열 목록에서 단어 등장 횟수를 세어 출력하세요.
- 입력: 없음. 예 `{ "슬라임", "오크", "슬라임" }`
- 출력: 단어별 빈도. 예 `슬라임 2` / `오크 1` 형태면 충분
- 조건: 키가 없으면 0에서 시작해 `++`

**문제 3**

- 문제: `deque<string>`에 앞·뒤로 문자열을 넣고 순서대로 출력하세요.
- 입력: 없음
- 출력: 넣은 결과가 앞→뒤 순으로 한 줄 또는 여러 줄
- 조건: `push_front`와 `push_back`을 각각 한 번 이상 사용

### 정답 포인트

- 문제 1: `m["이름"] = 점수;` 후 `cout << m["조회이름"];`
- 문제 2: `count[word] = count[word] + 1;` 또는 `++count[word];`
- 문제 3: `push_front` / `push_back` 후 범위 기반 `for`
- 공통: 접근 패턴으로 컨테이너 선택. 정렬 필요 여부가 `map` vs `unordered_map` 기준

---

[상위 문서로 돌아가기](./README.md)
