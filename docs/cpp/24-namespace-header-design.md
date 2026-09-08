# Chapter 24 네임스페이스와 헤더 설계

## 학습 목표

- 네임스페이스로 같은 이름 함수·클래스의 충돌을 막을 수 있다.
- 헤더에는 선언, 소스에는 정의를 두는 분리를 적용할 수 있다.
- `#pragma once`로 헤더 중복 포함을 막을 수 있다.

## 본문

### 24-1 네임스페이스 기초

파일이 늘면 `Init`, `Player`, `Load`처럼 짧은 이름이 겹치기 쉽습니다. **네임스페이스**는 이름을 구역으로 묶어 충돌을 줄이는 문법입니다. `namespace audio { ... }` 안에 둔 `Init`은 바깥에서 `audio::Init`으로 부릅니다.

`using namespace std;`는 입문 예제에서 편의를 위해 썼지만, 헤더 파일 최상단에 넓게 펼치면 포함하는 모든 파일에 이름이 새어 나갑니다. 헤더에서는 `std::`를 명시하거나, 필요한 이름만 좁게 쓰는 편이 안전합니다.

| 용어 | 의미 |
|---|---|
| 네임스페이스 | 식별자 충돌을 막는 이름 구역 |
| 한정 이름 | `audio::Init`처럼 구역을 붙여 부르는 이름 |

아래를 한 파일에 넣고 실행해, 같은 함수 이름이라도 구역이 다르면 공존하는지 확인해 보세요.

```cpp
#include <iostream>
using namespace std;

namespace audio
{
void Init()
{
    cout << "audio init" << endl;
}
}

namespace render
{
void Init()
{
    cout << "render init" << endl;
}
}

int main()
{
    audio::Init();
    render::Init();
    return 0;
}
```

**예상 출력**

```text
audio init
render init
```

**코드 해석**

- `audio::Init`과 `render::Init`은 이름이 같아도 구역이 다릅니다.
- `main`은 한정 이름으로 각각 호출합니다.
- 모듈 경계가 이름에 드러나 읽기가 쉬워집니다.

### 24-2 헤더와 소스 분리

프로젝트가 커지면 한 `.cpp`에 모든 코드를 두지 않습니다. **헤더**는 보통 `.h` 확장자를 쓰고 **선언**을 담습니다. **소스** `.cpp`는 `#include`로 헤더를 가져온 뒤 **정의**를 둡니다. 다른 파일은 헤더만 포함해 “무엇이 있는지”를 알고, 구현 세부는 소스에 감춥니다.

| 위치 | 넣는 것 | 예 |
|---|---|---|
| 헤더 `.h` | 선언 | `int Add(int a, int b);` |
| 소스 `.cpp` | 정의 | `int Add(int a, int b) { return a + b; }` |

학습 환경에서는 아직 한 파일 예제가 많지만, 아래처럼 **논리적으로** 헤더·소스가 나뉜 형태를 읽고 따라 적어 보세요. 실제로는 `math_util.h` / `math_util.cpp` / `main.cpp` 세 파일로 나누면 됩니다.

```cpp
// ===== math_util.h =====
#pragma once

namespace math_util
{
int Add(int a, int b);
}

// ===== math_util.cpp =====
#include "math_util.h"

namespace math_util
{
int Add(int a, int b)
{
    return a + b;
}
}

// ===== main.cpp =====
#include <iostream>
#include "math_util.h"
using namespace std;

int main()
{
    cout << math_util::Add(2, 3) << endl;
    return 0;
}
```

**예상 출력**

```text
5
```

**코드 해석**

- 헤더에는 `Add`의 선언만 있습니다. 본문은 소스에 있습니다.
- `main`은 헤더만 include하고 `math_util::Add`를 호출합니다.
- 구현을 바꿔도 선언이 같으면 `main` 쪽 수정이 줄어듭니다.

한 파일에서 바로 확인하려면, 위 세 블록의 내용을 순서대로 한 `.cpp`에 합쳐도 동작 확인은 가능합니다. 다만 실습 목표인 **파일 분리**를 하려면 헤더·소스를 실제 파일로 나누는 편이 좋습니다.

### 24-3 `#pragma once`와 중복 include

같은 헤더가 한 번역 단위에 여러 번 펼쳐지면, 클래스 정의가 두 번 나와 **재정의 오류**가 납니다. 헤더 A가 B를 포함하고, `main`이 A와 B를 모두 include하는 경우처럼 **간접 + 직접**이 겹칠 때 자주 납니다.

**`#pragma once`**는 “이 헤더는 한 번만 처리하라”고 컴파일러에 지시합니다. 헤더 맨 위에 한 줄만 두면 되어 입문에 적합합니다. 전통 방식은 `#ifndef` / `#define` / `#endif` **include guard**입니다. 둘 다 목적은 같고, 이 과정에서는 `#pragma once`를 기본으로 씁니다.

| 방식 | 장점 | 주의 |
|---|---|---|
| `#pragma once` | 짧고 실수가 적음 | 컴파일러 확장에 가깝지만 주요 환경은 지원 |
| `#ifndef` 가드 | 전처리 표준 문법으로 설명하기 쉬움 | 매크로 이름 오타·충돌 관리 필요 |

중복이 위험한 구조는 다음과 같습니다. `b.h`에 방지 장치가 없으면 `Item`이 두 번 정의될 수 있습니다.

```cpp
// a.h
#pragma once
#include "b.h"

// b.h  — 방지 장치가 없다고 가정하면 위험
class Item
{
};

// main.cpp
#include "a.h"
#include "b.h"  // 간접 + 직접
```

`b.h` 맨 위에 `#pragma once`를 두면 두 번째 포함은 무시됩니다. 보조로 `#ifndef` 형태도 읽을 수 있으면 됩니다.

```cpp
#ifndef MATH_UTIL_H
#define MATH_UTIL_H
namespace math_util
{
int Add(int a, int b);
}
#endif
```

이 장에서는 이름 구역과 헤더 경계까지입니다. 여러 파일을 한 번에 빌드하는 방법은 다음 장 CMake에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: `audio`, `render` 네임스페이스에 각각 `Init` 함수를 두고 호출하세요.
- 입력: 없음
- 출력:
  ```text
  audio init
  render init
  ```
- 조건: 함수 이름은 같고 네임스페이스만 다르게

**문제 2**

- 문제: `Add` 선언을 헤더, 정의를 소스처럼 분리한 뒤 `main`에서 호출하세요.
- 입력: 없음
- 출력:
  ```text
  5
  ```
  처럼 `2 + 3` 결과
- 조건: 헤더 상단 `#pragma once`. 네임스페이스 사용

**문제 3**

- 문제: 헤더 하나에 `#pragma once`를 넣고, 그 헤더를 두 경로로 include해도 재정의 오류가 나지 않음을 확인하세요.
- 입력: 없음
- 출력: 프로그램이 컴파일·실행되어 `ok` 한 줄
- 조건: 클래스 또는 인라인 가능한 선언을 헤더에 두고 이중 include 구조 만들기

### 정답 포인트

- 문제 1: `audio::Init();` / `render::Init();`
- 문제 2: `.h`에 선언, `.cpp`에 정의, `main`은 헤더만 include
- 문제 3: 공통 헤더 맨 위 `#pragma once` 후 직접·간접 include
- 공통: 충돌 위험 있으면 네임스페이스. 헤더에는 최소 선언

---

[상위 문서로 돌아가기](./README.md)
