# Chapter 11 static

## 학습 목표
- `static`의 의미와 사용 시점을 이해한다.
- 인스턴스 멤버와 정적 멤버를 구분할 수 있다.
- 상태 없는 기능은 **정적 유틸 메서드**로 묶을 수 있다.

## 본문

### 11-1 정적 필드/메서드

**`static`(정적)** 멤버는 어떤 객체 하나가 아니라 **클래스 자체**에 붙습니다. `Enemy.Count`처럼 **클래스 이름**으로 접근하고, 만든 객체 모두가 같은 필드를 공유합니다. “지금까지 몇 마리 생성됐는가?”처럼 **공용 집계**에 잘 맞습니다.

### 11-2 인스턴스 vs static

**인스턴스 멤버**는 객체마다 따로 있습니다. `enemy1.Hp`와 `enemy2.Hp`는 다른 값일 수 있습니다. **정적 멤버**는 프로그램(해당 클래스) 전체에서 하나라서, 객체 없이 `ClassName.Member`로 씁니다.

| 항목 | 인스턴스 멤버 | 정적(static) 멤버 |
|---|---|---|
| 소속 | 객체 | 클래스 |
| 접근 | 객체를 통해 접근 | 클래스명으로 접근 |
| 값 공유 | 객체마다 다름 | 전체 공유 |

### 11-3 유틸 클래스 설계

입력만 받아 결과만 돌려주고 **객체의 상태**가 필요 없으면 `static` 메서드로 두는 편이 단순합니다. 이미 쓰는 `Console.WriteLine`, `Math.Max`가 그런 예에 가깝습니다. 반대로 “이 적만의 체력”처럼 객체별 상태가 있으면 인스턴스 멤버가 맞습니다.

| 용어 | 의미 |
|---|---|
| 인스턴스 멤버 | 각 객체가 개별적으로 가지는 필드/메서드 |
| 정적 멤버 | 클래스 전체가 공유하는 필드/메서드 |

아래 코드를 실행해 `Enemy`를 두 번 만든 뒤 `Enemy.Count`가 `2`인지 확인해 보세요.

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

### 연습문제

**문제 1**
- 문제: 객체 생성 수를 정적 필드로 출력하세요.
- 입력: 없음(`new`를 3번 호출)
- 출력: `3`
- 조건: `static int` 사용, 생성자에서 증가

**문제 2**
- 문제: 두 수의 최대값을 반환하는 정적 메서드를 작성하세요.
- 입력: 없음(예: `Max(3, 9)` 호출)
- 출력: `9`
- 조건: `static` 메서드, 클래스명으로 호출

### 정답 포인트

- 문제 1: `public static int Count;` + 생성자 `Count++;` + `Console.WriteLine(TypeName.Count);`
- 문제 2: `public static int Max(int a, int b) { return a > b ? a : b; }`
- 공통: 공유·유틸에만 `static`, 객체별 상태는 인스턴스

---

[상위 문서로 돌아가기](./README.md)
