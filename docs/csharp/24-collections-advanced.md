# Chapter 24 컬렉션 심화

## 학습 목표
- 대표 컬렉션(`List`, `Dictionary`, `HashSet`, `Queue`, `Stack`)의 특성과 선택 기준을 이해한다.
- 문제 상황에 맞는 컬렉션을 선택하고 기본 연산을 구현할 수 있다.

## 본문

### 24-1 순차 컬렉션

**`List<T>`**는 순서·인덱스 접근이 필요할 때 기본 선택입니다. **`Queue<T>`**는 **FIFO**(먼저 들어온 것 먼저) 대기열, **`Stack<T>`**는 **LIFO**(나중에 들어온 것 먼저)로 실행 취소·역순 처리에 맞습니다. 빈 큐에서 `Dequeue`, 빈 스택에서 `Pop`하면 예외가 납니다.

### 24-2 키-값/집합 컬렉션

**`Dictionary<TKey, TValue>`**는 키로 값을 빠르게 찾을 때 씁니다. 같은 키를 두 번 추가하면 예외가 날 수 있어, 없을 때는 `TryGetValue` 등으로 방어합니다. **`HashSet<T>`**는 **중복 없는 집합**·존재 확인에 유리하고, 순서를 보장하지 않습니다.

### 24-3 선택 기준과 실수 포인트

“데이터 모양”보다 **조회·추가·삭제 패턴**으로 고릅니다. 역할을 섞어 한 컬렉션에 모두 담으면 코드가 커지므로, 인벤토리(Dictionary)·대기열(Queue)·중복 검사(HashSet)처럼 **역할별로 분리**하는 편이 낫습니다.

| 컬렉션 | 핵심 용도 | 장점 | 자주 하는 실수 |
|---|---|---|---|
| `List<T>` | 순서 있는 데이터 | 사용이 단순 | 중복/검색 최적화까지 기대 |
| `Dictionary<K,V>` | 키 기반 조회 | 조회가 빠름 | 키 중복 예외 처리 누락 |
| `HashSet<T>` | 중복 제거/존재 확인 | 중복 자동 방지 | 순서를 기대함 |
| `Queue<T>` | 대기열 처리 | FIFO 보장 | 빈 큐 `Dequeue` 예외 |
| `Stack<T>` | 실행 취소/역순 처리 | LIFO 보장 | 빈 스택 `Pop` 예외 |

| 용어 | 의미 |
|---|---|
| FIFO | First In First Out, 먼저 들어온 데이터가 먼저 나가는 방식 |
| LIFO | Last In First Out, 나중에 들어온 데이터가 먼저 나가는 방식 |

아래 코드를 실행해 고유 점수 개수와 포션 수량이 출력되는지 확인해 보세요.

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

        var jobs = new Queue<string>();
        jobs.Enqueue("Save");
        jobs.Enqueue("Load");

        Console.WriteLine($"unique: {uniqueScores.Count}");
        Console.WriteLine($"potion: {inventory["Potion"]}");
        Console.WriteLine($"next job: {jobs.Dequeue()}");
    }
}
```

**예상 출력**

```text
unique: 2
potion: 3
next job: Save
```

**코드 해석**
- `HashSet`이 `80` 중복을 없애 개수 `2`입니다.
- `Dictionary`로 `"Potion"` 수량을 조회합니다.
- `Queue`에서 `Dequeue`하면 먼저 넣은 `"Save"`가 나옵니다(FIFO).

### 연습문제

**문제 1**
- 문제: `Queue<string>`으로 작업 대기열을 만들고 처리 순서를 출력하세요.
- 입력: 없음(예: `A`, `B`, `C` Enqueue)
- 출력:
  ```text
  A
  B
  C
  ```
- 조건: `Enqueue`/`Dequeue`

**문제 2**
- 문제: 닉네임 목록에서 중복을 제거하고 개수(또는 목록)를 출력하세요.
- 입력: 없음(예: `kim`, `lee`, `kim`)
- 출력: `2` (고유 개수) 또는 `kim lee`
- 조건: `HashSet<string>`

**문제 3**
- 문제: `Dictionary`로 아이템 수량을 저장하고 키를 조회하세요.
- 입력: 없음(예: `Sword=1` 저장 후 `"Sword"` / `"Shield"` 조회)
- 출력: `1` / `없음` (키 없을 때 메시지)
- 조건: 키 미존재 시 기본 메시지 (`TryGetValue` 또는 `ContainsKey`)

### 정답 포인트

- 문제 1: Enqueue 후 while Count>0 Dequeue 출력
- 문제 2: `new HashSet<string>(names)` 후 Count/출력
- 문제 3: `TryGetValue`로 없으면 `"없음"`
- 공통: 접근 패턴으로 선택, 빈 컬렉션·키 없음 방어

---

[상위 문서로 돌아가기](./README.md)
