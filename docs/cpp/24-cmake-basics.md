# Chapter 24 CMake 빌드 기초

## 학습 목표
- CMake의 역할과 기본 사용 흐름을 이해한다.
- 멀티 파일 프로젝트를 CMake로 빌드할 수 있다.

## 세부 주제
### 24-1 CMake란?
- 빌드 설정 자동화

### 24-2 `CMakeLists.txt` 기본
- 프로젝트/타깃 선언

### 24-3 빌드 실행 흐름
- configure -> build -> run

## 실습 체크리스트
- `main.cpp + util.cpp` 프로젝트를 CMake로 빌드해 실행한다.

## 본문

### 24-1 CMake란?
- CMake는 컴파일 옵션과 파일 구성을 플랫폼별 빌드 시스템으로 생성하는 도구입니다.
- IDE/컴파일러가 달라도 같은 설정 파일을 재사용할 수 있습니다.

약어 설명(IDE):
- **IDE**는 **Integrated Development Environment**의 약자입니다.
- 한국어로는 "통합 개발 환경"이며, 코드 편집/빌드/디버깅을 한곳에서 제공하는 도구(예: Visual Studio)를 뜻합니다.
- CMake를 쓰면 IDE가 달라도 같은 빌드 설정을 공유해 협업이 쉬워집니다.

### 24-2 `CMakeLists.txt` 기본
- `cmake_minimum_required`, `project`, `add_executable`이 기본 골격입니다.
- 소스 파일 목록을 타깃에 명시해 빌드 단위를 정의합니다.

### 24-3 빌드 실행 흐름
- configure: 빌드 설정 생성
- build: 실제 컴파일/링크
- run: 실행 파일 테스트

용어 설명:
| 용어 | 의미 |
|---|---|
| configure | 빌드 설정을 생성하는 단계 |
| target | 실행 파일/라이브러리 같은 빌드 결과 단위 |

빌드 단계 요약:
| 단계 | 명령 예시 | 결과 |
|---|---|---|
| 설정 생성 | `cmake -S . -B build` | 빌드 파일 생성 |
| 컴파일 | `cmake --build build` | 실행 파일 생성 |
| 실행 | `./build/app` (환경별 경로 상이) | 프로그램 실행 |

예시 코드:
```cmake
cmake_minimum_required(VERSION 3.20)
project(CppSample)
add_executable(app main.cpp util.cpp)
```

예상 결과:
```text
build 폴더에 빌드 결과가 생성되고 app 실행 파일이 만들어진다.
```

코드 해석:
- `project(CppSample)`로 프로젝트 이름을 선언합니다.
- `add_executable`에서 실행 파일 이름(`app`)과 소스 목록을 연결합니다.
- 소스 파일이 늘어나면 여기에 추가해 빌드 범위를 확장합니다.

연습문제:
1. 문제 1 - 멀티 파일 빌드
   - 문제: `main.cpp`, `calc.cpp`로 구성한 프로젝트를 CMake로 빌드하세요.
   - 입력: 없음
   - 출력: 계산 결과
   - 조건: `add_executable`에 파일 2개 이상 포함
2. 문제 2 - 빌드 타입 분리
   - 문제: Debug/Release 빌드를 각각 생성해 실행 파일 크기 차이를 확인하세요.
   - 입력: 없음
   - 출력: 빌드 로그/파일 크기
   - 힌트: CMake 설정 옵션 확인

정답 포인트:
- 소스 루트와 빌드 출력 디렉터리를 분리
- CMake 설정 파일 하나로 여러 환경 빌드 재사용 가능

---

[상위 문서로 돌아가기](./README.md)
