# Chapter 12 파일 입출력

## 학습 목표

- `ofstream`으로 텍스트 파일을 쓸 수 있다.
- `ifstream`과 `>>`·`getline`으로 파일을 읽을 수 있다.
- 스트림 열기 실패를 검사하고 경로를 변수로 관리할 수 있다.

## 본문

### 12-1 파일 쓰기

텍스트를 파일로 남기려면 `<fstream>`의 **`ofstream`**을 씁니다. `ofstream fout(path);`처럼 경로를 넘겨 파일을 엽니다. 열린 뒤에는 `cout`과 비슷하게 `fout << "내용";`으로 씁니다. 경로에 파일이 있으면 **내용을 덮어쓰는** 경우가 많습니다. 경로 문자열은 변수로 분리해 두면 읽기·쓰기에서 같은 파일을 가리키기 쉽습니다.

열기에 실패하면 이후 쓰기는 의미가 없습니다. 연 직후 **`if (!fout)`**으로 상태를 확인합니다. 다 쓰면 `fout.close();`로 닫거나, 객체가 범위를 벗어날 때 닫히게 둡니다. 입문에서는 명시적으로 `close()`를 호출하는 습관도 좋습니다.

아래를 실행해, 저장 후 안내 문구가 나오는지 확인해 보세요. 실행한 폴더에 `save.txt` 파일이 생깁니다.

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string path = "save.txt";
    ofstream fout(path);
    if (!fout)
    {
        cout << "열기 실패" << endl;
        return 1;
    }

    fout << "Player=Kim,Level=5";
    fout.close();
    cout << "저장 완료" << endl;
    return 0;
}
```

**예상 출력**

```text
저장 완료
```

**코드 해석**

- `path`에 파일 이름 `"save.txt"`를 둡니다.
- `ofstream`으로 파일을 열고, 실패하면 메시지를 남긴 뒤 종료합니다.
- `fout << ...`로 문자열을 쓴 뒤 `close()`하고, 콘솔에는 완료 메시지만 출력합니다.

### 12-2 파일 읽기

**`ifstream`**은 파일에서 읽어 오는 입력 스트림입니다. `fin >> name >> hp;`처럼 `cin`과 비슷한 **추출**로 공백 단위 값을 읽을 수 있습니다. 한 줄 전체를 문자열로 받으려면 **`getline(fin, line)`**을 씁니다. 짧은 설정 한 덩어리는 `>>` 또는 한 번 `getline`, 줄마다 레코드면 `getline`을 반복하는 편이 맞습니다.

| API | 역할 | 이럴 때 |
|---|---|---|
| `ofstream` | 파일에 쓰기 | 저장·세이브 |
| `ifstream` | 파일에서 읽기 | 불러오기 |
| `>>` | 공백으로 나뉜 값 | 이름·숫자 한 줄 |
| `getline` | 한 줄 전체 `string` | 줄 단위 데이터 |

먼저 앞에서 만든 `save.txt`를 한 덩어리로 읽는 예입니다. `getline`으로 첫 줄을 읽어 출력해 보세요.

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string path = "save.txt";
    ifstream fin(path);
    if (!fin)
    {
        cout << "파일 없음" << endl;
        return 1;
    }

    string text;
    getline(fin, text);
    cout << text << endl;
    return 0;
}
```

**예상 출력**

```text
Player=Kim,Level=5
```

**코드 해석**

- `ifstream`으로 같은 경로를 엽니다.
- `getline`으로 한 줄을 읽어 `text`에 담습니다.
- 저장했던 문자열이 그대로 출력됩니다.

이어서 이름과 숫자를 `>>`로 나누어 읽는 예입니다. 파일을 쓴 뒤 바로 읽어 확인해 보세요.

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string path = "player.txt";

    ofstream fout(path);
    if (!fout)
    {
        return 1;
    }
    fout << "Kim 120\n";
    fout.close();

    ifstream fin(path);
    if (!fin)
    {
        return 1;
    }

    string name;
    int hp = 0;
    fin >> name >> hp;
    cout << name << ' ' << hp << endl;
    return 0;
}
```

**예상 출력**

```text
Kim 120
```

**코드 해석**

- `ofstream`으로 이름과 체력을 한 줄에 저장합니다.
- `ifstream`으로 다시 열어 `>>`로 `name`과 `hp`를 읽습니다.
- 콘솔에 `Kim 120`이 출력됩니다.

여러 줄이면 파일을 만든 뒤 `getline`을 `while`로 돌립니다.

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string path = "lines.txt";
    ofstream fout(path);
    fout << "apple\nbanana\ncherry\n";
    fout.close();

    ifstream fin(path);
    string line;
    while (getline(fin, line))
    {
        cout << line << endl;
    }
    return 0;
}
```

**예상 출력**

```text
apple
banana
cherry
```

**코드 해석**

- `\n`으로 줄바꿈을 넣어 세 줄을 저장합니다.
- `while (getline(fin, line))`이 줄을 읽을 수 있는 동안 반복합니다.
- 각 줄을 한 줄씩 출력합니다.

### 12-3 열기 실패와 경로

경로가 틀리거나 파일이 없으면 읽기 스트림이 열리지 않습니다. 읽기 전에 **`if (!fin)`**으로 걸러 `파일 없음` 같은 메시지를 보여 줍니다. **상대 경로**는 실행 위치를 기준으로, **절대 경로**는 드라이브부터 전체 경로입니다. 같은 `save.txt`라도 어디서 실행하느냐에 따라 찾는 폴더가 달라질 수 있으니, 학습 단계에서는 경로를 변수 하나로 통일해 두고 실행 위치를 확인합니다.

| 용어 | 의미 |
|---|---|
| 파일 스트림 | 파일과 프로그램 사이 데이터 이동 통로 |
| 상대 경로 | 현재 실행 위치를 기준으로 해석되는 경로 |
| 절대 경로 | 드라이브부터 시작하는 전체 경로 |

| 단계 | 핵심 작업 | 체크 |
|---|---|---|
| 열기 | `ofstream` / `ifstream` | `if (!stream)` |
| 처리 | `<<` / `>>` / `getline` | 형식 일치 |
| 닫기 | `close()` 또는 범위 종료 | 쓰기 후 닫기 |

아래를 실행해, 없는 파일을 열 때 실패 분기로 `파일 없음`이 나오는지 확인해 보세요.

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string path = "missing.txt";
    ifstream fin(path);
    if (!fin)
    {
        cout << "파일 없음" << endl;
        return 0;
    }

    string text;
    getline(fin, text);
    cout << text << endl;
    return 0;
}
```

**예상 출력**

```text
파일 없음
```

**코드 해석**

- `missing.txt`가 없으면 `!fin`이 참이 됩니다.
- 읽지 않고 `파일 없음`만 출력합니다.
- 파일이 있을 때만 `getline`으로 내용을 출력하는 구조입니다.

예외 문법은 이후 장에서 다룹니다. 지금은 스트림 상태 검사와 경로 변수로 실패를 다루는 것으로 충분합니다.

### 연습문제

**문제 1**

- 문제: 사용자 입력 문장 1줄을 파일에 저장하세요.
- 입력: 예 `hello save`
- 출력: `저장 완료`
- 조건: 경로를 변수로 두고 `ofstream` 사용. 열기 실패 검사

**문제 2**

- 문제: 저장한 파일을 다시 읽어 출력하세요.
- 입력: 없음. 문제 1과 같은 경로
- 출력: 파일 내용. 예 `hello save`
- 조건: 없으면 `파일 없음` 출력. `if (!fin)` 사용

**문제 3**

- 문제: 두 줄짜리 파일을 만들고 줄마다 출력하세요.
- 입력: 없음. 내용 예 `red`와 `blue` 두 줄
- 출력:
  ```text
  red
  blue
  ```
- 조건: `ofstream`으로 저장한 뒤 `getline`과 반복문으로 출력

### 정답 포인트

- 문제 1: `string path = "memo.txt"; ofstream fout(path);` → `getline(cin, line); fout << line;` → `저장 완료`
- 문제 2: `ifstream fin(path); if (!fin) cout << "파일 없음"; else { getline(fin, line); cout << line; }`
- 문제 3: `fout`에 `red\nblue\n` → `while (getline(fin, line)) cout << line << endl;`
- 공통: 경로를 변수로 두고, 열기 직후 `if (!stream)`을 먼저 생각합니다.

---

[상위 문서로 돌아가기](./README.md)
