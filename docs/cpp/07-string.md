# Chapter 07 문자열 다루기

## 학습 목표

- `string`으로 문자열을 만들고 `+`로 결합할 수 있다.
- `cin >>`와 `getline`의 공백 처리 차이를 구분해 쓴다.
- `size`, `substr`, `find`로 길이·부분 문자열·검색을 할 수 있다.

## 본문

### 07-1 `string` 기초

앞 장에서 이름 같은 값을 `string` 변수에 담아 보았습니다. **`string`**은 길이가 바뀔 수 있는 문자열 객체입니다. C의 `char` 배열보다 결합·길이 확인이 단순한 경우가 많습니다. 쓰려면 `#include <string>`이 필요합니다.

문자열끼리 **`+`**로 이어 붙일 수 있습니다. `string full = last + first;`처럼 쓰면 두 조각을 한 문자열로 만듭니다.

```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string last = "Kim";
    string first = "Jinwoo";
    string full = last + first;

    cout << full << endl;
    cout << "길이: " << full.size() << endl;
    return 0;
}
```

**예상 출력**

```text
KimJinwoo
길이: 9
```

**코드 해석**

- `last + first`로 두 문자열을 이어 `full`에 담습니다.
- `size()`는 글자 개수를 돌려줍니다.

공백을 넣고 싶으면 `" "`를 사이에 끼우면 됩니다. `last + " " + first`처럼 씁니다.

### 07-2 `cin >>`와 `getline`

| 방식 | 공백 처리 | 이럴 때 |
|---|---|---|
| `cin >> s` | 공백 전까지만 읽음 | 단어·한 토큰 |
| `getline(cin, s)` | 공백 포함 한 줄 전체 | 문장·띄어쓰기 있는 이름 |

`cin >>`는 앞 장 입력과 같습니다. 문장 전체를 받으려면 **`getline(cin, s)`**를 씁니다.

```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string title;
    cout << "문장 입력: ";
    getline(cin, title);

    cout << "길이: " << title.size() << endl;
    return 0;
}
```

**예상 출력** — 입력 `Cpp Basic`인 경우

```text
문장 입력: Cpp Basic
길이: 9
```

**코드 해석**

- `getline`은 공백을 포함해 Enter 전까지 한 줄을 읽습니다.
- `cin >>`만 썼다면 `Cpp`까지만 들어갔을 것입니다.

**주의:** 같은 프로그램에서 `cin >>`로 숫자를 읽은 뒤 `getline`을 쓰면, 줄 끝에 남은 Enter 때문에 빈 줄이 먼저 읽힐 수 있습니다. 입문 연습에서는 **한 예제에서 입력 방식을 하나만** 쓰는 편이 안전합니다.

### 07-3 `substr`과 `find`

**`substr(시작, 길이)`**는 부분 문자열을 잘라 냅니다. 인덱스는 배열처럼 **0부터**입니다. `title.substr(0, 3)`은 앞에서 3글자입니다.

**`find(찾을글)`**은 그 글자가 처음 나타나는 위치를 돌려줍니다. 못 찾으면 **`string::npos`**라는 특수 값이 나옵니다. 포함 여부는 `find` 결과가 `npos`와 같은지로 판단합니다.

```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string title = "Cpp Basic";

    cout << "앞 3글자: " << title.substr(0, 3) << endl;

    if (title.find("Basic") != string::npos)
    {
        cout << "Basic 포함" << endl;
    }
    else
    {
        cout << "Basic 없음" << endl;
    }

    return 0;
}
```

**예상 출력**

```text
앞 3글자: Cpp
Basic 포함
```

**코드 해석**

- `substr(0, 3)`으로 `"Cpp"`를 자릅니다.
- `find("Basic")`이 성공하면 `npos`가 아니므로 `Basic 포함`이 출력됩니다.

이 장 범위는 **`string` 결합 · 한 줄 입력 · 길이·부분·검색**입니다. 함수로 나누어 다루는 법은 **다음 장**에서 이어집니다.

### 연습문제

**문제 1**

- 문제: 성과 이름을 입력받아 사이에 공백을 넣어 전체 이름을 출력하세요.
- 입력: 성 1개, 이름 1개
- 출력: 결합된 전체 이름
- 조건: `+`로 결합. `cin >>` 사용 가능

**문제 2**

- 문제: `getline`으로 문장 하나를 받고 길이를 출력하세요.
- 입력: 공백 포함 가능 한 줄
- 출력: 길이 숫자 1개
- 조건: `getline`과 `size()` 사용

**문제 3**

- 문제: 문장과 검색 단어를 입력받아 포함 여부를 출력하세요.
- 입력: 문장 1개, 단어 1개
- 출력: 포함 / 미포함 메시지
- 조건: `find`와 `string::npos` 비교

### 정답 포인트

- 문제 1: `full = last + " " + first;`
- 문제 2: `getline(cin, s);` → `cout << s.size();`
- 문제 3: `if (s.find(word) != string::npos)`
- 공통: 공백 포함 문장은 `getline`, 단어만이면 `cin >>`

---

[상위 문서로 돌아가기](./README.md)
