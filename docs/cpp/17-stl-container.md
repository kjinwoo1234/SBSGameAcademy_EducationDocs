# Chapter 17 STL 컨테이너 심화

## 학습 목표
- 주요 STL 컨테이너의 특징과 선택 기준을 이해한다.
- `vector`, `deque`, `map`, `unordered_map`의 차이를 설명할 수 있다.

## 세부 주제
### 17-1 순차 컨테이너
- `vector`, `deque`, `list` 개요

### 17-2 연관 컨테이너
- `map`, `set`

### 17-3 해시 기반 컨테이너
- `unordered_map`, `unordered_set`

## 실습 체크리스트
- 아이템 인벤토리를 `map` 또는 `unordered_map`으로 구현한다.

## 본문

### 17-1 순차 컨테이너
- 순차 컨테이너는 삽입 순서 기반으로 데이터를 관리합니다.
- `vector`는 가장 기본 선택이며 임의 접근 성능이 좋습니다.

### 17-2 연관 컨테이너
- `map`은 키 정렬 상태를 유지하며 키-값 쌍을 저장합니다.
- 정렬 순회가 필요하면 `map`이 유리합니다.

### 17-3 해시 기반 컨테이너
- `unordered_map`은 해시 기반으로 평균 빠른 탐색을 제공합니다.
- 대신 정렬 순서를 보장하지 않습니다.

컨테이너 선택 가이드:
| 컨테이너 | 정렬 | 탐색 특징 | 적합한 상황 |
|---|---|---|---|
| `vector` | 없음 | 인덱스 접근 빠름 | 순차 데이터/반복 처리 |
| `map` | 키 정렬 | 로그 시간 탐색 | 정렬된 키 순회 |
| `unordered_map` | 없음 | 평균 상수 시간 탐색 | 빠른 키 조회 |

용어 설명:
| 용어 | 의미 |
|---|---|
| STL | C++ 표준 템플릿 라이브러리 집합 |
| 해시(hash) | 키를 빠르게 찾기 위한 값 변환 방식 |

예시 코드:
```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

int main() {
    unordered_map<string, int> itemCount;
    itemCount["potion"] = 3;
    itemCount["elixir"] = 1;
    cout << itemCount["potion"] << endl;
    return 0;
}
```

예상 출력:
```text
3
```

코드 해석:
- `unordered_map<string, int>`는 문자열 키와 정수 값을 저장합니다.
- `itemCount["potion"] = 3`으로 키-값 쌍을 삽입/갱신합니다.
- 키를 통해 빠르게 값을 조회하는 인벤토리 구조에 적합합니다.

연습문제:
1. 문제 1 - 점수표 구현
   - 문제: 이름-점수 매핑을 저장하고 특정 이름 점수를 출력하세요.
   - 입력: 이름 3개, 점수 3개, 조회 이름 1개
   - 출력: 조회 점수
   - 조건: `map` 또는 `unordered_map` 사용
2. 문제 2 - 빈도수 계산
   - 문제: 문자열 배열에서 단어 등장 횟수를 계산하세요.
   - 입력: 단어 목록
   - 출력: 단어별 빈도
   - 힌트: 키가 없으면 0에서 시작해 증가

정답 포인트:
- 컨테이너는 데이터 접근 패턴으로 선택
- 정렬 필요 여부가 `map` vs `unordered_map` 선택 기준

---

[상위 문서로 돌아가기](./README.md)
