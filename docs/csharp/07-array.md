# Chapter 07 배열

## 학습 목표

- 1차원 배열 선언·초기화·순회를 익힌다.
- 인덱스 범위와 예외 위험을 이해한다.

## 본문

### 07-1 배열 선언

변수는 값 하나를 담는 상자에 가깝습니다. 그런데 점수처럼 같은 종류의 값이 여러 개면 `score1`, `score2`처럼 변수를 계속 늘리기 어렵습니다. 이럴 때 쓰는 것이 **배열**입니다. **배열**은 같은 자료형의 값을 **연속된 칸**에 모아 두고, 이름 하나로 그 칸들을 다루는 자료 구조입니다.

먼저 칸 개수만 정해 만드는 방법입니다. 아래 한 줄의 의미를 하나씩 짚어 보겠습니다.

```csharp
int[] scores = new int[5];
```

- `int[]` — 자료형 뒤에 `[]`를 붙여 “정수를 담는 배열”이라고 선언합니다.
- `scores` — 배열의 이름입니다.
- `=` — 오른쪽에 만든 배열을 `scores`에 연결합니다.
- `new int[5]` — 정수 칸 **5개**짜리 배열을 **실제로 만듭니다**. `new`가 “만든다”는 뜻이고, `[5]`가 칸 개수입니다.

이 줄까지 실행하면 칸은 생겼지만, 아직 점수를 넣지 않았으므로 각 칸에는 기본값 `0`이 들어 있습니다. `int[] scores;`처럼 이름만 두고 `new`로 칸을 만들지 않으면 `scores[0] = 80;`을 쓸 수 없습니다. 칸을 만든 다음에 `scores[0] = 80;`처럼 값을 넣습니다.

아래를 `Main`에 넣고 실행해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] scores = new int[5];
        scores[0] = 80;
        scores[1] = 95;
        Console.WriteLine(scores[0]);
    }
}
```

**예상 출력**

```text
80
```

**코드 해석**
- `new int[5]`로 칸을 만든 뒤 `scores[0]`에 `80`을 넣습니다.
- `scores[0]`을 출력하면 `80`이 나옵니다.

이어서 처음부터 값을 넣어 만드는 방법입니다. **초기화 목록**을 씁니다. 아래 한 줄의 의미를 하나씩 짚어 보겠습니다.

```csharp
int[] scores = { 60, 85, 70, 90 };
```

- `int[] scores` — 정수 배열 `scores`를 선언합니다.
- `{ 60, 85, 70, 90 }` — 넣을 값을 중괄호 안에 나열합니다.

적어 둔 값의 개수가 곧 배열 크기입니다. 값이 네 개이므로 칸도 네 개이고, `new int[4]`를 따로 적을 필요는 없습니다.

아래를 `Main`에 넣고 실행해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] scores = { 60, 85, 70, 90 };
        Console.WriteLine(scores[0]);
    }
}
```

**예상 출력**

```text
60
```

**코드 해석**
- 첫 칸 `scores[0]`은 `60`입니다.

마지막으로 칸 개수를 확인하는 방법입니다. 배열 이름 뒤에 **`.Length`**를 붙이면 됩니다. `scores.Length`는 `scores`가 몇 칸인지 알려 줍니다.

아래를 `Main`에 넣고 실행해 보세요. `new int[5]`로 만든 배열과 초기화 목록으로 만든 배열의 칸 개수가 어떻게 다른지 비교해 봅니다.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] a = new int[5];
        Console.WriteLine(a.Length);

        int[] b = { 60, 85, 70, 90 };
        Console.WriteLine(b.Length);
    }
}
```

**예상 출력**

```text
5
4
```

**코드 해석**
- `a`는 `new int[5]`이므로 `a.Length`는 `5`입니다.
- `b`는 값이 네 개이므로 `b.Length`는 `4`입니다.

### 07-2 인덱스 접근

**인덱스**는 각 칸의 위치 번호이며 **0부터** 시작합니다. 네 칸짜리 배열이면 쓸 수 있는 번호는 `0`, `1`, `2`, `3`입니다. `scores[0]`은 첫 칸이고, 마지막 칸은 `scores[scores.Length - 1]`입니다. 칸 개수가 `Length`이므로 마지막 번호는 그보다 하나 작은 값입니다.

아래를 실행해 첫 값과 마지막 값을 확인해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] scores = { 80, 95, 70, 100 };
        Console.WriteLine(scores[0]);
        Console.WriteLine(scores[scores.Length - 1]);
    }
}
```

**예상 출력**

```text
80
100
```

**코드 해석**
- `scores[0]`은 첫 값 `80`입니다.
- `Length`는 `4`이므로 `scores[scores.Length - 1]`은 `scores[3]`이고, 마지막 값 `100`입니다.

존재하지 않는 번호로 접근하면 안 됩니다. 네 칸짜리에서 `scores[4]`는 범위 밖입니다. 실행 중 **`IndexOutOfRangeException`**이 나고, **예외**는 프로그램 실행 중 발생한 오류 상황입니다. 아래처럼 주석으로만 알아 두고, 켜서 확인해도 됩니다.

```csharp
// Console.WriteLine(scores[4]); // Length는 4 → 인덱스 4는 범위 밖
```

| 용어 | 의미 |
|---|---|
| 인덱스 | 배열에서 각 원소의 위치 번호. 0부터 시작 |
| 예외 | 프로그램 실행 중 발생한 오류 상황 |

### 07-3 순회

배열의 모든 칸을 살펴보는 것을 **순회**라고 합니다. 먼저 인덱스가 필요할 때의 **`for`**입니다. `i`로 몇 번째 칸인지 다루면서 값을 볼 수 있습니다.

아래를 `Main`에 넣고, 인덱스와 값이 함께 출력되는지 확인해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] scores = { 80, 95, 70, 100 };
        for (int i = 0; i < scores.Length; i++)
        {
            Console.WriteLine($"{i}: {scores[i]}");
        }
    }
}
```

**예상 출력**

```text
0: 80
1: 95
2: 70
3: 100
```

**코드 해석**
- `i`는 `0`부터 `Length` 직전까지입니다. 네 칸이면 `0`~`3`입니다.
- `scores[i]`로 인덱스와 값을 함께 다룹니다.

이어서 값만 순서대로 꺼내는 **`foreach`**입니다. 형태는 `foreach (int s in scores)`처럼 씁니다. `int s`는 꺼낸 값을 담을 변수이고, `in scores`는 어느 배열에서 꺼낼지입니다. 합·평균처럼 읽기만 할 때 문장이 짧아집니다.

아래를 `Main`에 넣고 실행해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] scores = { 80, 95, 70, 100 };
        int sum = 0;
        foreach (int s in scores)
        {
            sum += s;
        }
        Console.WriteLine(sum);
        Console.WriteLine((double)sum / scores.Length);
    }
}
```

**예상 출력**

```text
345
86.25
```

**코드 해석**
- `foreach`로 모든 점수를 더해 합을 만듭니다.
- `scores.Length`로 개수를 나누고, `(double)`으로 실수 나눗셈을 유지해 평균을 구합니다.

정리하면, 인덱스가 필요하면 `for`, 값만 순서대로 읽으면 `foreach`가 맞습니다.

| 방식 | 적합한 상황 | 장점 | 주의점 |
|---|---|---|---|
| `for` | 인덱스가 필요할 때 | `i`로 위치·조건 제어 | `0`부터 `Length - 1`까지 범위 실수 |
| `foreach` | 값만 순서대로 읽을 때 | 문장이 간결 | 루프 변수로 원본 칸을 바꾸기 어려움 |

### 연습문제

**문제 1**

- 문제: 정수 배열에서 최대값을 출력하세요.
- 입력: 없음. 코드에 `{ 3, 9, 2, 7 }`처럼 배열을 두고 시작해도 됩니다.
- 출력: `9`
- 조건: 반복문으로 직접 비교. `Max` 메서드 사용 금지

**문제 2**

- 문제: 배열에서 짝수 원소 개수를 구하세요.
- 입력: 없음. 예 배열은 `{ 1, 2, 3, 4, 6 }`
- 출력: `3`
- 조건: `% 2 == 0`으로 짝수 판정, 반복문으로 개수 누적

**문제 3**

- 문제: 배열의 각 칸을 `인덱스: 값` 형태로 한 줄씩 출력하세요.
- 입력: 없음. 예 배열은 `{ 10, 20, 30 }`
- 출력:
  ```text
  0: 10
  1: 20
  2: 30
  ```
- 조건: `for`와 `Length` 사용

### 정답 포인트

- 문제 1: `max`를 첫 원소로 두고 `for` 또는 `foreach`로 더 큰 값이면 갱신
- 문제 2: 짝수일 때마다 카운터 `++`
- 문제 3: `for (int i = 0; i < arr.Length; i++)` 안에서 `$"{i}: {arr[i]}"`
- 공통: 인덱스는 `0` 시작, `Length`를 넘기지 않기

---

[상위 문서로 돌아가기](./README.md)