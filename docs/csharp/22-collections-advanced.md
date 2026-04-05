# Chapter 22 컬렉션 심화

## 학습 목표
- 대표 컬렉션(`List`, `Dictionary`, `HashSet`, `Queue`, `Stack`)의 특성과 선택 기준을 이해한다.
- 문제 상황에 맞는 컬렉션을 선택하고 기본 연산을 구현할 수 있다.

## 세부 주제
### 22-1 순차 컬렉션
- `List<T>`, `Queue<T>`, `Stack<T>`

### 22-2 키-값/집합 컬렉션
- `Dictionary<TKey, TValue>`, `HashSet<T>`

### 22-3 선택 기준과 실수 포인트
- 데이터 접근 패턴 기반 선택

## 실습 체크리스트
- 인벤토리/중복 체크/작업 대기열을 서로 다른 컬렉션으로 구현한다.

## 본문

### 22-1 순차 컬렉션
- `List<T>`는 인덱스 접근과 순차 처리에 가장 많이 쓰는 기본 컬렉션입니다.
- `Queue<T>`는 선입선출(FIFO), `Stack<T>`은 후입선출(LIFO) 구조로 작업 순서 제어에 유용합니다.

### 22-2 키-값/집합 컬렉션
- `Dictionary<TKey, TValue>`는 키로 빠르게 값을 찾는 데 적합합니다.
- `HashSet<T>`는 중복 없는 값 집합을 관리할 때 유리합니다.

### 22-3 선택 기준과 실수 포인트
- "어떻게 조회/추가/삭제할지"를 먼저 정하고 컬렉션을 선택해야 합니다.
- 컬렉션 종류를 섞어 쓰면 코드가 길어질 수 있으므로 역할을 분리하는 것이 좋습니다.

컬렉션 선택 표:
| 컬렉션 | 핵심 용도 | 장점 | 자주 하는 실수 |
|---|---|---|---|
| `List<T>` | 순서 있는 데이터 | 사용이 단순 | 중복/검색 최적화까지 기대 |
| `Dictionary<K,V>` | 키 기반 조회 | 조회가 빠름 | 키 중복 예외 처리 누락 |
| `HashSet<T>` | 중복 제거/존재 확인 | 중복 자동 방지 | 순서를 기대함 |
| `Queue<T>` | 대기열 처리 | FIFO 보장 | 빈 큐 `Dequeue` 예외 |
| `Stack<T>` | 실행 취소/역순 처리 | LIFO 보장 | 빈 스택 `Pop` 예외 |

용어 설명:
| 용어 | 의미 |
|---|---|
| FIFO | First In First Out, 먼저 들어온 데이터가 먼저 나가는 방식 |
| LIFO | Last In First Out, 나중에 들어온 데이터가 먼저 나가는 방식 |

예시 코드:
```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        var scores = new List<int> { 80, 95, 80 };
        var uniqueScores = new HashSet<int>(scores);

        var inventory = new Dictionary<string, int>();
        inventory["Potion"] = 3;
        inventory["Elixir"] = 1;

        Console.WriteLine($"unique: {uniqueScores.Count}");
        Console.WriteLine($"potion: {inventory["Potion"]}");
    }
}
```

예상 출력:
```text
unique: 2
potion: 3
```

코드 해석:
- `HashSet<int>`로 중복 점수를 제거해 고유 값 개수를 계산합니다.
- `Dictionary<string, int>`로 아이템 이름을 키로 사용해 수량을 빠르게 조회합니다.

연습문제:
1. 문제 1 - 대기열 시스템
   - 문제: `Queue<string>`으로 작업 대기열을 만들고 처리 순서를 출력하세요.
   - 입력: 작업 이름 3개 이상
   - 출력: 처리 순서
   - 조건: `Enqueue`/`Dequeue` 사용
2. 문제 2 - 중복 닉네임 검사
   - 문제: 닉네임 목록에서 중복을 제거하고 최종 목록을 출력하세요.
   - 입력: 닉네임 문자열 목록
   - 출력: 중복 제거 결과
   - 힌트: `HashSet<string>` 사용
3. 문제 3 - 아이템 수량 조회
   - 문제: `Dictionary`로 아이템 수량을 저장하고 특정 키를 조회하세요.
   - 입력: 아이템 이름, 수량
   - 출력: 조회 결과
   - 조건: 키 미존재 시 기본 메시지 처리

정답 포인트:
- 컬렉션은 "데이터 형태"보다 "접근 패턴"으로 선택
- 빈 컬렉션 연산, 키 미존재 케이스를 항상 방어 처리

---

[상위 문서로 돌아가기](./README.md)
