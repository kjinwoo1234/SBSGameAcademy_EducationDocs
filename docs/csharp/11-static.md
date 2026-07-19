# Chapter 11 static

## 학습 목표
- `static`의 의미와 사용 시점을 이해한다.
- 인스턴스 멤버와 정적 멤버를 구분할 수 있다.
- 상태 없는 기능은 정적 유틸 메서드로 묶을 수 있다.

## 본문

### 11-1 정적 필드/메서드

**`static`** 멤버는 어떤 객체 하나가 아니라 **클래스 자체**에 붙습니다. `Enemy.Count`처럼 **클래스 이름**으로 접근하고, 만든 객체 모두가 같은 필드를 공유합니다. “지금까지 몇 마리 생성됐는가?”처럼 **공용 집계**에 잘 맞습니다.

아래를 실행해 `Enemy`를 두 번 만든 뒤 `Enemy.Count`가 `2`인지 확인해 보세요.

```csharp
using System;

class Enemy
{
    public static int Count = 0;

    public Enemy()
    {
        Count++;
    }
}

class Program
{
    static void Main()
    {
        Enemy a = new Enemy();
        Enemy b = new Enemy();
        Console.WriteLine(Enemy.Count);
    }
}
```

**예상 출력**

```text
2
```

**코드 해석**
- `Count`는 정적 필드라 모든 `Enemy`가 공유합니다.
- 생성자마다 `Count++`가 실행되어 객체 두 개 → 출력 `2`입니다.
- `a.Count`가 아니라 `Enemy.Count`로 접근하는 것이 정적 멤버의 일반적인 쓰기입니다.

### 11-2 인스턴스 vs static

**인스턴스 멤버**는 객체마다 따로 있습니다. `enemy1.Hp`와 `enemy2.Hp`는 다른 값일 수 있습니다. **정적 멤버**는 프로그램에서 해당 클래스당 하나라서, 객체 없이 `ClassName.Member`로 씁니다.

| 항목 | 인스턴스 멤버 | 정적 멤버 |
|---|---|---|
| 소속 | 객체 | 클래스 |
| 접근 | 객체를 통해 접근 | 클래스명으로 접근 |
| 값 공유 | 객체마다 다름 | 전체 공유 |

아래를 실행해 두 적의 체력은 다르고, `Count`만 공유되는지 확인해 보세요.

```csharp
using System;

class Enemy
{
    public static int Count = 0;
    public int Hp;

    public Enemy(int hp)
    {
        Hp = hp;
        Count++;
    }
}

class Program
{
    static void Main()
    {
        Enemy a = new Enemy(30);
        Enemy b = new Enemy(50);
        Console.WriteLine(a.Hp);
        Console.WriteLine(b.Hp);
        Console.WriteLine(Enemy.Count);
    }
}
```

**예상 출력**

```text
30
50
2
```

**코드 해석**
- `Hp`는 인스턴스 필드라 `a`와 `b`가 서로 다른 값을 가집니다.
- `Count`는 정적이라 두 번 생성해도 공유 값이 `2`입니다.
- 체력은 `a.Hp`처럼 객체로, 개수는 `Enemy.Count`처럼 클래스명으로 읽습니다.

### 11-3 유틸 클래스 설계

입력만 받아 결과만 돌려주고 **객체의 상태**가 필요 없으면 `static` 메서드로 두는 편이 단순합니다. 이미 쓰는 `Console.WriteLine`이 그런 예에 가깝습니다. 반대로 “이 적만의 체력”처럼 객체별 상태가 있으면 인스턴스 멤버가 맞습니다.

| 용어 | 의미 |
|---|---|
| 인스턴스 멤버 | 각 객체가 개별적으로 가지는 필드/메서드 |
| 정적 멤버 | 클래스 전체가 공유하는 필드/메서드 |

아래를 실행해 객체를 만들지 않고 `Calc.Max`만으로 큰 수가 나오는지 확인해 보세요.

```csharp
using System;

class Calc
{
    public static int Max(int a, int b)
    {
        if (a > b)
        {
            return a;
        }
        return b;
    }
}

class Program
{
    static void Main()
    {
        Console.WriteLine(Calc.Max(3, 9));
    }
}
```

**예상 출력**

```text
9
```

**코드 해석**
- `Max`는 정적 메서드라 `new Calc()` 없이 `Calc.Max(3, 9)`로 호출합니다.
- `if`로 더 큰 수를 골라 반환합니다. 객체가 들고 있을 상태가 없습니다.

이 장에서는 정적 필드·인스턴스와 구분·유틸 메서드까지 다룹니다. 상속은 다음 장에서 이어갑니다.

### 연습문제

**문제 1**
- 문제: 객체 생성 수를 정적 필드로 출력하세요.
- 입력: 없음. `new`를 3번 호출
- 출력: `3`
- 조건: `static int` 사용, 생성자에서 증가

**문제 2**
- 문제: 두 수의 최대값을 반환하는 정적 메서드를 작성하세요.
- 입력: 없음. 예로 `Max(3, 9)` 호출
- 출력: `9`
- 조건: `static` 메서드, 클래스명으로 호출. `if`로 비교

**문제 3**
- 문제: `Enemy` 두 개를 체력 `10`, `20`으로 만들고, 각 체력과 `Enemy.Count`를 순서대로 출력하세요.
- 입력: 없음
- 출력:
  ```text
  10
  20
  2
  ```
- 조건: `Hp`는 인스턴스, `Count`는 `static`

### 정답 포인트

- 문제 1: `public static int Count;` + 생성자 `Count++;` + `Console.WriteLine(TypeName.Count);`
- 문제 2: `public static int Max(int a, int b)` 안에서 `if (a > b) return a; else return b;`
- 문제 3: 인스턴스 `Hp` 각각 출력 후 `Enemy.Count` 출력
- 공통: 공유·유틸에만 `static`, 객체별 상태는 인스턴스

---

[상위 문서로 돌아가기](./README.md)
