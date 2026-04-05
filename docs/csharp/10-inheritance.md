# Chapter 10 상속

## 학습 목표
- 상속 문법과 목적을 이해한다.
- 기반 클래스와 파생 클래스 관계를 설계할 수 있다.

## 세부 주제
### 10-1 상속 기본 문법
- `: BaseClass`

### 10-2 생성자 호출
- 기반 생성자 호출

### 10-3 접근 제한자와 상속
- `protected`

## 실습 체크리스트
- `Character` 기반 클래스와 `Warrior` 파생 클래스를 작성한다.

## 본문

### 10-1 상속 기본 문법
- 상속은 공통 기능을 재사용해 중복 코드를 줄입니다.
- 파생 클래스는 기반 클래스 기능을 확장할 수 있습니다.

### 10-2 생성자 호출
- 파생 객체 생성 시 기반 생성자가 먼저 실행됩니다.
- `base(...)` 키워드로 기반 생성자 인자를 전달할 수 있습니다.

### 10-3 접근 제한자와 상속
- `private`는 파생 클래스 직접 접근 불가입니다.
- `protected`는 파생 클래스에서 접근 가능합니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 기반 클래스(base class) | 공통 기능을 제공하는 부모 클래스 |
| 파생 클래스(derived class) | 기반 클래스를 확장한 자식 클래스 |

예시 코드:
```csharp
using System;

class Character
{
    protected int hp;
    public Character(int hp) { this.hp = hp; }
}

class Warrior : Character
{
    public Warrior(int hp) : base(hp) { }
    public void PrintHp() => Console.WriteLine(hp);
}

class Program
{
    static void Main()
    {
        Warrior w = new Warrior(120);
        w.PrintHp();
    }
}
```

예상 출력:
```text
120
```

코드 해석:
- `Warrior : Character`로 상속 관계를 선언합니다.
- `base(hp)`를 통해 기반 클래스 생성자에 값을 전달합니다.

연습문제:
1. 문제 1 - Monster 상속
   - 문제: `Monster` 기반 클래스와 `BossMonster` 파생 클래스를 작성하세요.
   - 입력: 체력 값
   - 출력: 체력 출력
   - 조건: `base` 생성자 호출
2. 문제 2 - protected 활용
   - 문제: 기반 클래스의 `protected` 필드를 파생 클래스 메서드에서 출력하세요.
   - 입력: 없음
   - 출력: 필드 값
   - 힌트: `private`와 접근 차이 비교

정답 포인트:
- 공통 기능은 기반 클래스에 배치
- 접근 제한자 설계가 유지보수성을 좌우

---

[상위 문서로 돌아가기](./README.md)
