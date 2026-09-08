# Chapter 25 CMake 빌드 기초

## 학습 목표

- CMake가 하는 일과 configure·build 단계를 구분할 수 있다.
- `CMakeLists.txt` 최소 골격으로 실행 파일 타깃을 정의할 수 있다.
- 소스 여러 개를 한 타깃에 묶어 빌드·실행할 수 있다.

## 본문

### 25-1 CMake란?

**CMake**는 소스와 컴파일 옵션을 읽어, Visual Studio·Ninja·Makefile 같은 **빌드 시스템 파일**을 만들어 주는 도구입니다. 운영체제나 IDE가 달라도 같은 `CMakeLists.txt`를 재사용할 수 있습니다.

**IDE**는 Integrated Development Environment의 약자로, 편집·빌드·디버깅을 한곳에서 하는 환경입니다. CMake를 쓰면 IDE가 바뀌어도 프로젝트 구성을 다시 손으로 맞추는 일이 줄어듭니다.

| 용어 | 의미 |
|---|---|
| configure | 빌드 설정을 생성하는 단계 |
| build | 실제 컴파일·링크를 수행하는 단계 |
| target | 실행 파일·라이브러리 같은 빌드 결과 단위 |

소스 폴더와 빌드 결과 폴더를 나누는 것이 기본입니다. 소스 트리에 오브젝트 파일이 섞이지 않아 정리하기 쉽습니다.

### 25-2 `CMakeLists.txt` 기본

프로젝트 루트에 **`CMakeLists.txt`**를 둡니다. 입문에서 꼭 나오는 명령은 다음 세 가지입니다.

1. `cmake_minimum_required(VERSION 3.20)` — 최소 CMake 버전
2. `project(이름)` — 프로젝트 이름
3. `add_executable(타깃 소스...)` — 실행 파일 타깃과 소스 목록

아래는 `main.cpp`와 `util.cpp`를 묶어 `app`을 만드는 예입니다.

```cmake
cmake_minimum_required(VERSION 3.20)
project(CppSample)
add_executable(app main.cpp util.cpp)
```

**코드 해석**

- `project(CppSample)`로 이 빌드 묶음의 이름을 정합니다.
- `add_executable`의 첫 인자는 실행 파일 이름, 이후는 컴파일할 소스입니다.
- 파일이 늘면 같은 줄에 소스 이름을 추가합니다.

함께 둘 소스 예시는 다음과 같습니다.

```cpp
// util.h
#pragma once
int Add(int a, int b);

// util.cpp
#include "util.h"
int Add(int a, int b)
{
    return a + b;
}

// main.cpp
#include <iostream>
#include "util.h"
using namespace std;

int main()
{
    cout << Add(2, 3) << endl;
    return 0;
}
```

**예상 출력** (빌드 후 `app` 실행)

```text
5
```

### 25-3 빌드 실행 흐름

터미널에서 프로젝트 루트로 이동한 뒤, **설정 생성**과 **빌드**를 순서대로 실행합니다. Windows·macOS·Linux 모두 같은 형태를 씁니다. 실행 파일 경로만 환경마다 조금 다릅니다.

| 단계 | 명령 예 | 결과 |
|---|---|---|
| 설정 생성 | `cmake -S . -B build` | `build` 폴더에 빌드 파일 생성 |
| 컴파일 | `cmake --build build` | 실행 파일 생성 |
| 실행 | `build/app` 또는 `build/Debug/app.exe` 등 | 프로그램 출력 확인 |

`-S`는 소스 경로, `-B`는 빌드 출력 경로입니다. 처음 `build` 폴더가 없으면 만들어 줍니다.

Debug와 Release를 나누고 싶으면 configure 때 빌드 타입을 지정할 수 있습니다.

```text
cmake -S . -B build-debug -DCMAKE_BUILD_TYPE=Debug
cmake --build build-debug

cmake -S . -B build-release -DCMAKE_BUILD_TYPE=Release
cmake --build build-release
```

Visual Studio 생성기를 쓰면 구성이 IDE 안에 Debug/Release로 나뉘므로, 위 `CMAKE_BUILD_TYPE`은 단일 구성 생성기에서 더 직접적으로 드러납니다. 입문 실습에서는 **소스와 빌드 폴더 분리**와 **`add_executable`에 파일 나열**만 확실히 하면 됩니다.

이 장에서는 CMake로 멀티 파일 실행 파일을 만드는 것까지입니다. 디버깅·테스트 습관은 다음 장에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: `main.cpp`와 `calc.cpp`를 포함한 프로젝트를 CMake로 빌드하세요.
- 입력: 없음. `calc`에 더하기 함수를 두고 `main`에서 호출
- 출력: 계산 결과 한 줄. 예
  ```text
  7
  ```
- 조건: `CMakeLists.txt`의 `add_executable`에 소스 2개 이상

**문제 2**

- 문제: `cmake -S . -B build`와 `cmake --build build`로 빌드한 뒤 실행 파일을 실행하세요.
- 입력: 없음
- 출력: 문제 1과 같은 프로그램 출력
- 조건: 소스 디렉터리와 `build` 출력 디렉터리 분리

**문제 3**

- 문제: Debug용·Release용 빌드 폴더를 각각 만들어 빌드해 보세요.
- 입력: 없음
- 출력: 두 구성 모두 실행되어 같은 결과가 나오면 통과. 가능하면 실행 파일 크기 차이를 메모
- 조건: 폴더 이름을 구분해 configure. 예 `build-debug`, `build-release`

### 정답 포인트

- 문제 1: `add_executable(app main.cpp calc.cpp)` 형태
- 문제 2: `-S . -B build` 후 `--build build`, 그다음 실행 파일 경로로 실행
- 문제 3: 빌드 타입·폴더를 나눠 각각 configure/build
- 공통: 설정 파일 하나, 출력은 `build` 쪽으로. 소스가 늘면 `add_executable`만 갱신

---

[상위 문서로 돌아가기](./README.md)
