# Chapter 02 변수와 자료형

## 학습 목표
- C# **기본 자료형**(`int`, `double`, `bool`, `char`, `string`)의 역할과 예시 값을 구분한다.
- 변수 **선언·초기화·재할당**을 올바르게 작성한다.
- **`var`(타입 추론)**와 명시적 타입 선언의 차이를 이해한다.
- **`$"...{변수}..."` 문자열 보간**으로 변수와 문장을 함께 출력할 수 있다.

## 본문

### 02-1 기본 자료형

**변수**는 값을 담는 **이름이 붙은 상자**입니다. **자료형(type, 타입)**은 그 상자에 “어떤 종류의 값”을 넣을지 정하는 규칙입니다. C#은 타입이 맞지 않으면 대부분 컴파일 단계에서 막아 주므로, “이 값은 정수인가, 문자열인가”를 먼저 고르는 습관이 중요합니다.

앞 장에서 배운 `Console.WriteLine(...)`으로 담아 둔 값을 화면에 확인할 수 있습니다.

| 타입 | 예시 값 | 한 줄 설명 | 자주 쓰는 곳 |
|---|---|---|---|
| `int` | `10`, `-3` | 정수 | 점수, 인덱스, 횟수 |
| `double` | `3.14`, `0.8` | 실수 | 평균, 비율, 좌표 |
| `bool` | `true`, `false` | 참/거짓 | 플래그, 조건 결과 |
| `char` | `'A'`, `'1'` | 문자 1개 | 등급, 키 한 글자 |
| `string` | `"Hello"` | 문자열 | 이름, 경로, 안내 문구 |

`char`는 작은따옴표 `'A'`, `string`은 큰따옴표 `"A"`를 씁니다. 한 글자라도 `"A"`는 문자열이라 `char grade = "A";`는 컴파일 오류입니다.

지금부터 자료형별로 값을 담아 출력해 보겠습니다.

```csharp
using System;

class Program
{
    static void Main()
    {
        int level = 5;
        double ratio = 0.8;
        bool isClear = true;
        char grade = 'A';
        string name = "Kim";

        Console.WriteLine(level);
        Console.WriteLine(ratio);
        Console.WriteLine(isClear);
        Console.WriteLine(grade);
        Console.WriteLine(name);
    }
}
```

**예상 출력**

```text
5
0.8
True
A
Kim
```

**코드 해석**
- 타입마다 넣을 수 있는 값의 형태가 다릅니다. `int`는 정수, `double`은 소수, `char`는 문자 1개, `string`은 글자 덩어리입니다.
- `bool`을 출력하면 코드의 `true`/`false`와 달리 보통 `True` / `False`처럼 보입니다.
- `double` 값 `0.8`은 콘솔에 `0.8`로 보이는 경우가 많습니다.

### 02-2 변수 선언/초기화

선언은 `타입 이름;` 또는 `타입 이름 = 값;`처럼 쓰고, 처음 값을 넣는 것을 **초기화**라고 합니다. C# 지역 변수는 초기화하지 않은 채 읽으면 컴파일 오류가 나므로, “쓰기 전에 값을 넣었는지”를 확인합니다.

같은 타입을 여러 개 선언할 때는 `int x = 1, y = 2;`처럼 한 줄에 나열할 수 있습니다. 이미 선언된 변수에 새 값을 넣는 것은 **재할당**입니다. `const int MaxHp = 100;`처럼 `const`를 붙이면 이후 재할당이 막혀, 바뀌면 안 되는 값에 씁니다. **지금은 “재할당을 막는 형태가 있다” 정도만** 기억하면 됩니다. 상수 설계는 나중에 더 다룹니다.

변수 이름은 영문·숫자·밑줄(`_`)을 쓰고 **숫자로 시작할 수 없습니다.** `int`, `for`, `class` 같은 **예약어**는 이름으로 쓸 수 없습니다. C#에서는 보통 지역 변수에 **camelCase**(`playerHp`)를 많이 씁니다.

**자주 하는 실수**
- 타입과 값 불일치: `int n = 3.5;`
- 초기화 전 사용: `int n; Console.WriteLine(n);`
- `char` / `string` 따옴표 혼동

아래 코드를 `Main`에 넣고 실행해 예상 출력과 비교해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        int score;
        score = 0;
        score = score + 10;

        const int MaxScore = 100;

        Console.WriteLine(score);
        Console.WriteLine(MaxScore);
    }
}
```

**예상 출력**

```text
10
100
```

**코드 해석**
- `score`는 0으로 시작한 뒤 10으로 바뀝니다.
- `MaxScore`는 재할당이 막힌 상수입니다.

### 02-3 `var`

**`var`**는 “타입이 없다”는 뜻이 아니라, **오른쪽 초기값을 보고 컴파일러가 타입을 추론**한다는 뜻입니다. `var name = "Kim";`은 컴파일 시점에 `string`으로 고정되고, 이후 `name = 10;`처럼 다른 타입을 넣으면 오류입니다.

입문에서는 **명시적 타입**(`int`, `string` 등)을 먼저 익히고, `var`는 오른쪽만 봐도 타입이 분명할 때 쓰는 편이 안전합니다.

| 쓰는 형태 | 예 | 이럴 때 | 입문 권장 |
|---|---|---|---|
| 명시적 타입 | `string nick = "Kim";` | 타입을 코드에 바로 보이게 할 때 | **먼저 익히기** |
| `var` | `var nick = "Kim";` | 오른쪽 초기값만 봐도 타입이 분명할 때 | 익숙해진 뒤 |

아래 코드를 `Main`에 넣고 실행해 예상 출력과 비교해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        var nick = "Kim";
        var level = 5;

        Console.WriteLine(nick);
        Console.WriteLine(level);
    }
}
```

**예상 출력**

```text
Kim
5
```

**코드 해석**
- `var`로 선언해도 타입은 컴파일 시점에 확정됩니다.

### 02-4 변수 값을 문장에 넣어 출력하기

변수 값을 문장 안에 섞으려면 **`$"..."`** 앞에 `$`를 붙이고, `{이름}` 자리에 변수 이름을 넣습니다. 이것을 **문자열 보간**이라고 합니다.

아래 코드를 `Main`에 넣고 실행해 예상 출력과 비교해 보세요.

```csharp
using System;

class Program
{
    static void Main()
    {
        string nick = "Jinwoo";
        int score = 100;

        Console.WriteLine($"닉네임: {nick}");
        Console.WriteLine($"점수: {score}");
    }
}
```

**예상 출력**

```text
닉네임: Jinwoo
점수: 100
```

**코드 해석**
- `$"점수: {score}"`에서 `{score}` 자리에 `100`이 들어갑니다.

### 연습문제

**문제 1**
- 문제: `int`, `double`, `bool` 변수를 선언하고 각각 출력하세요.
- 입력: 없음
- 출력: 첫 줄 `10`, 둘째 줄 `0.5`, 셋째 줄 `True` (값은 달라도 타입만 맞으면 됨. 자가 확인용 예시)
- 조건: 각 타입 최소 1개

**문제 2**
- 문제: `var`로 문자열과 정수를 각각 선언해 출력하세요.
- 입력: 없음
- 출력: 첫 줄 `Kim`, 둘째 줄 `3` (예시)
- 조건: 초기값으로 타입이 분명해야 함

**문제 3**
- 문제: `string` 이름과 `int` 점수를 선언한 뒤 `$"이름={name}, 점수={score}"`로 한 줄 출력하세요.
- 입력: 없음
- 출력: 예) `이름=Kim, 점수=90`
- 조건: 문자열 보간 사용

### 정답 포인트

- 문제 1: 타입별 선언 후 `Console.WriteLine`
- 문제 2: `var title = "..."; var count = 3;`
- 문제 3: `$"이름={name}, 점수={score}"`
- 공통: `var`도 컴파일 시점에 타입이 고정됨

---

[상위 문서로 돌아가기](./README.md)
