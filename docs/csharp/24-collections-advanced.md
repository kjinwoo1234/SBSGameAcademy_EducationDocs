# Chapter 24 컬렉션 심화

## 학습 목표
- `List`, `Queue`, `Stack`, `Dictionary`, `HashSet`의 용도를 구분한다.
- 접근 패턴에 맞는 컬렉션을 고르고 `Add`·`Enqueue`·`Push`·`TryGetValue` 등 기본 연산을 작성할 수 있다.
- 빈 컬렉션·없는 키처럼 자주 나는 실수를 피한다.

## 본문

### 24-1 List

**`List<T>`**는 같은 타입 값을 **순서대로** 담고, 인덱스로 꺼낼 때 기본으로 쓰는 컬렉션입니다. `List<int> scores = new List<int>();`처럼 만들고, `scores.Add(80);`으로 끝에 값을 붙입니다. `scores[0]`은 첫 값이고, `scores.Count`는 현재 개수입니다. 배열의 `Length`와 비슷한 역할이지만, 리스트는 나중에 원소를 더 넣어도 됩니다.

아래를 `Main`에 넣고 실행해, 추가한 값과 개수가 어떻게 나오는지 확인해 보세요.

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        List<int> scores = new List<int>();
        scores.Add(80);
        scores.Add(95);
        scores.Add(70);

        Console.WriteLine(scores[0]);
        Console.WriteLine(scores.Count);
    }
}
```

**예상 출력**

```text
80
3
```

**코드 해석**
- `new List<int>()`로 정수 리스트를 만듭니다.
- `Add(80)`처럼 괄호 안에 넣을 값을 넘기고, 문장 끝 `;`로 명령을 끝냅니다.
- `scores[0]`은 인덱스 `0`의 값, `Count`는 원소 개수 `3`입니다.

### 24-2 Queue와 Stack

**`Queue<T>`**는 **FIFO** 대기열입니다. 먼저 넣은 값이 먼저 나갑니다. `Enqueue("Save")`로 넣고, `Dequeue()`로 앞에서 꺼냅니다. **`Stack<T>`**는 **LIFO**입니다. 나중에 넣은 값이 먼저 나갑니다. `Push("Undo")`로 넣고, `Pop()`으로 꼭대기에서 꺼냅니다. 빈 큐에서 `Dequeue()`, 빈 스택에서 `Pop()`을 호출하면 예외가 납니다.

| 컬렉션 | 넣는 메서드 | 꺼내는 메서드 | 나가는 순서 |
|---|---|---|---|
| `Queue<T>` | `Enqueue` | `Dequeue` | 먼저 넣은 것 먼저 |
| `Stack<T>` | `Push` | `Pop` | 나중에 넣은 것 먼저 |

| 용어 | 의미 |
|---|---|
| FIFO | First In First Out. 먼저 들어온 데이터가 먼저 나감 |
| LIFO | Last In First Out. 나중에 들어온 데이터가 먼저 나감 |

먼저 큐입니다. 아래를 실행해 `"Save"`가 `"Load"`보다 먼저 나오는지 확인해 보세요.

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        Queue<string> jobs = new Queue<string>();
        jobs.Enqueue("Save");
        jobs.Enqueue("Load");

        Console.WriteLine(jobs.Dequeue());
        Console.WriteLine(jobs.Dequeue());
    }
}
```

**예상 출력**

```text
Save
Load
```

**코드 해석**
- `Enqueue("Save")`는 대기열 끝에 문자열을 넣습니다.
- `Dequeue()`는 앞에서 하나 꺼내 반환합니다. 인자 없는 `()`입니다.
- 두 번 꺼내면 넣은 순서 그대로 `Save`, `Load`입니다.

이어서 스택입니다. 아래를 실행해 나중에 넣은 `"B"`가 먼저 나오는지 확인해 보세요.

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        Stack<string> undo = new Stack<string>();
        undo.Push("A");
        undo.Push("B");

        Console.WriteLine(undo.Pop());
        Console.WriteLine(undo.Pop());
    }
}
```

**예상 출력**

```text
B
A
```

**코드 해석**
- `Push("A")`는 스택 꼭대기에 값을 올립니다.
- `Pop()`은 꼭대기 값을 꺼내 반환합니다.
- 나중에 넣은 `"B"`가 먼저 나옵니다.

### 24-3 Dictionary와 HashSet

**`Dictionary<TKey, TValue>`**는 **키**로 **값**을 찾을 때 씁니다. `inventory["Potion"] = 3;`처럼 키에 값을 넣고, 같은 키로 읽습니다. 없는 키를 `inventory["Shield"]`처럼 바로 읽으면 예외가 날 수 있습니다. 없을 수 있을 때는 `TryGetValue("Shield", out int amount)`처럼 씁니다. 첫 인자는 찾을 키, `out int amount`는 찾으면 담을 변수입니다. 반환값이 `true`면 키가 있었고, `false`면 없었습니다.

**`HashSet<T>`**는 **중복 없는 집합**입니다. 같은 값을 두 번 넣어도 한 번만 남고, 순서를 보장하지 않습니다. 존재 확인·중복 제거에 맞습니다.

아래를 실행해 포션 수량과 없는 키 메시지, 고유 점수 개수를 확인해 보세요.

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        Dictionary<string, int> inventory = new Dictionary<string, int>();
        inventory["Potion"] = 3;
        inventory["Elixir"] = 1;

        Console.WriteLine(inventory["Potion"]);

        if (inventory.TryGetValue("Shield", out int amount))
        {
            Console.WriteLine(amount);
        }
        else
        {
            Console.WriteLine("없음");
        }

        List<int> scores = new List<int> { 80, 95, 80 };
        HashSet<int> uniqueScores = new HashSet<int>(scores);
        Console.WriteLine(uniqueScores.Count);
    }
}
```

**예상 출력**

```text
3
없음
2
```

**코드 해석**
- `inventory["Potion"]`으로 키 `"Potion"`의 값을 읽습니다.
- `TryGetValue("Shield", out int amount)`는 키가 없어 `false`이고, `else`에서 `"없음"`을 출력합니다.
- `HashSet<int>(scores)`는 리스트를 받아 중복 `80`을 한 번만 남겨 `Count`가 `2`입니다.

### 24-4 선택 기준

데이터 “모양”보다 **조회·추가·삭제 패턴**으로 고릅니다. 인벤토리는 키로 찾는 `Dictionary`, 저장 대기열은 `Queue`, 닉네임 중복 검사는 `HashSet`처럼 **역할별로 컬렉션을 나누는** 편이 낫습니다. 한 리스트에 모든 역할을 섞으면 코드가 커지고 실수도 늘기 쉽습니다.

| 컬렉션 | 핵심 용도 | 장점 | 자주 하는 실수 |
|---|---|---|---|
| `List<T>` | 순서 있는 데이터 | 사용이 단순 | 중복·빠른 검색까지 기대 |
| `Dictionary<K,V>` | 키 기반 조회 | 조회가 빠름 | 키 없을 때 예외 처리 누락 |
| `HashSet<T>` | 중복 제거·존재 확인 | 중복 자동 방지 | 출력 순서를 기대함 |
| `Queue<T>` | 대기열 처리 | FIFO 보장 | 빈 큐에서 `Dequeue` |
| `Stack<T>` | 실행 취소·역순 처리 | LIFO 보장 | 빈 스택에서 `Pop` |

이 장에서는 대표 컬렉션의 선택과 기본 연산까지입니다. 더 복잡한 동시성 컬렉션은 다루지 않습니다.

### 연습문제

**문제 1**
- 문제: `Queue<string>`으로 작업 대기열을 만들고 처리 순서를 한 줄씩 출력하세요.
- 입력: 없음. 코드에 `A`, `B`, `C`를 `Enqueue`해도 됩니다.
- 출력:
  ```text
  A
  B
  C
  ```
- 조건: `Enqueue`와 `Dequeue` 사용

**문제 2**
- 문제: 닉네임 목록에서 중복을 제거한 뒤 고유 개수를 출력하세요.
- 입력: 없음. 예 목록은 `kim`, `lee`, `kim`
- 출력: `2`
- 조건: `HashSet<string>` 사용

**문제 3**
- 문제: `Dictionary`로 아이템 수량을 저장하고 키를 조회하세요. 없는 키면 안내 문구를 출력하세요.
- 입력: 없음. 예: `Sword`에 `1` 저장 후 `"Sword"`와 `"Shield"` 조회
- 출력:
  ```text
  1
  없음
  ```
- 조건: 키 미존재 시 `"없음"`. `TryGetValue` 또는 `ContainsKey` 사용

### 정답 포인트

- 문제 1: `Enqueue` 후 `while (queue.Count > 0)` 안에서 `Dequeue` 출력
- 문제 2: `new HashSet<string>(names)` 후 `Count` 출력
- 문제 3: `TryGetValue`로 없으면 `"없음"` 출력
- 공통: 접근 패턴으로 컬렉션을 고르고, 빈 컬렉션·없는 키를 방어한다

---

[상위 문서로 돌아가기](./README.md)
