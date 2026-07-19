# Chapter 07 배열

## 학습 목표
- 1차원 배열 선언·초기화·순회를 익힌다.
- 인덱스 범위와 예외 위험을 이해한다.

## 본문

### 07-1 배열 선언

**배열**은 같은 타입 값을 연속된 칸에 모아 두는 자료 구조입니다. 크기를 정해 만들 때는 `int[] scores = new int[5];`처럼 씁니다. 여기서 `new int[5]`는 정수 칸 5개를 새로 만든다는 뜻이고, `5`가 칸 개수입니다. 처음부터 값을 넣으려면 `int[] scores = { 80, 95, 70, 100 };`처럼 **초기화 목록**을 쓸 수 있습니다. 배열 이름 뒤의 `[]`는 원소에 접근할 때 인덱스를 쓴다는 표시입니다.

아래를 `Main`에 넣고 실행해, 첫 칸과 칸 개수가 어떻게 나오는지 확인해 보세요.

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
        Console.WriteLine(scores.Length);
    }
}
```

**예상 출력**

```text
80
5
```

**코드 해석**
- `new int[5]`로 칸 5개를 만듭니다. 아직 값을 넣지 않은 칸은 `0`입니다.
- `scores[0] = 80;`처럼 대괄호 안 번호로 특정 칸에 값을 넣습니다.
- `scores.Length`는 칸 개수 `5`입니다.

### 07-2 인덱스 접근

**인덱스**는 각 칸의 위치 번호이며 **0부터** 시작합니다. 네 칸짜리 배열이면 유효 인덱스는 `0`부터 `3`까지입니다. `scores[0]`은 첫 값이고, `scores[scores.Length - 1]`은 마지막 값입니다. 길이 `Length`와 “마지막 인덱스 = 길이 - 1”을 항상 함께 떠올리세요.

범위를 벗어나 `scores[4]`처럼 접근하면 실행 중 **`IndexOutOfRangeException`**이 납니다. **예외**는 프로그램 실행 중 발생한 오류 상황입니다.

| 용어 | 의미 |
|---|---|
| 인덱스 | 배열에서 각 원소의 위치 번호. 0부터 시작 |
| 예외 | 프로그램 실행 중 발생한 오류 상황 |

아래를 실행해 첫 값과 마지막 값을 확인해 보세요. 주석 처리된 `scores[4]`는 켜면 예외가 납니다. 지금은 주석으로만 알아 두세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] scores = { 80, 95, 70, 100 };
        Console.WriteLine(scores[0]);
        Console.WriteLine(scores[scores.Length - 1]);
        // Console.WriteLine(scores[4]); // Length는 4 → 인덱스 4는 범위 밖
    }
}
```

**예상 출력**

```text
80
100
```

**코드 해석**
- 초기화 목록으로 칸 4개를 만들면 `Length`는 `4`이고, 마지막 인덱스는 `3`입니다.
- `scores[4]`는 유효 범위가 아니라 예외입니다.

### 07-3 순회

배열의 모든 칸을 살펴보는 것을 **순회**라고 합니다. **`for`**는 인덱스를 직접 다루므로 몇 번째 칸인지가 필요할 때 유리합니다. **`foreach`**는 값을 순서대로 꺼내기만 할 때 문장이 짧습니다. 형태는 `foreach (int s in scores)`처럼 씁니다. `int s`는 꺼낸 값을 담을 변수이고, `in scores`는 어느 배열에서 꺼낼지입니다. 평균·합처럼 읽기만 하면 `foreach`로 충분하고, 특정 인덱스를 바꾸거나 건너뛸 때는 `for`가 맞습니다.

| 방식 | 적합한 상황 | 장점 | 주의점 |
|---|---|---|---|
| `for` | 인덱스가 필요할 때 | `i`로 위치·조건 제어 | `0`부터 `Length - 1`까지 범위 실수 |
| `foreach` | 값만 순서대로 읽을 때 | 문장이 간결 | 루프 변수로 원본 칸을 바꾸기 어려움 |

먼저 인덱스가 필요할 때의 `for`입니다. 아래를 `Main`에 넣고, 몇 번째 칸인지와 값이 함께 출력되는지 확인해 보세요.

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

이어서 값만 읽어 합과 평균을 구하는 `foreach`입니다. 아래를 `Main`에 넣고 실행해 보세요.

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
