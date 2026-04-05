# Chapter 11 다형성과 인터페이스

## 학습 목표
- 오버라이딩 기반 다형성을 이해한다.
- 기반 타입으로 여러 파생 객체를 처리할 수 있다.
- 인터페이스 기반 설계를 적용할 수 있다.

## 세부 주제
### 11-1 가상 메서드
- `virtual`, `override`

### 11-2 다형성 호출
- 기반 참조로 파생 동작 실행

### 11-3 추상 클래스
- 구현 강제

### 11-4 인터페이스
- 계약 중심 설계

## 실습 체크리스트
- `Enemy` 기반 타입 컬렉션으로 여러 공격 동작을 호출한다.

## 본문

### 11-1 가상 메서드
- 기반 메서드를 `virtual`로 선언하면 파생 클래스에서 `override`로 재정의할 수 있습니다.
- 실행 시 실제 객체 타입 기준으로 메서드가 선택됩니다.

### 11-2 다형성 호출
- `Enemy e = new Orc();`처럼 기반 타입 변수로 파생 객체를 다룰 수 있습니다.
- 호출 코드는 단순해지고 확장은 쉬워집니다.

### 11-3 추상 클래스
- 공통 규약만 제공하고 구현을 강제할 때 `abstract`를 사용합니다.

### 11-4 인터페이스
- 인터페이스는 "무엇을 할 수 있는지"만 정의하고 구현은 클래스에 맡깁니다.
- 클래스는 여러 인터페이스를 동시에 구현할 수 있어 확장성이 높습니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 다형성(polymorphism) | 같은 호출로 객체마다 다른 동작을 수행하는 특성 |
| 추상 클래스 | 직접 생성할 수 없고 파생 클래스 구현을 강제하는 클래스 |
| 인터페이스(interface) | 구현 없이 멤버 시그니처만 정의한 계약 타입 |

예시 코드:
```csharp
using System;

interface IAttackable
{
    void Attack();
}

abstract class Enemy : IAttackable
{
    public abstract void Attack();
}

class Slime : Enemy
{
    public override void Attack() => Console.WriteLine("Slime Attack");
}

class Orc : Enemy
{
    public override void Attack() => Console.WriteLine("Orc Attack");
}

class Program
{
    static void Main()
    {
        IAttackable[] enemies = { new Slime(), new Orc() };
        foreach (IAttackable e in enemies) e.Attack();
    }
}
```

예상 출력:
```text
Slime Attack
Orc Attack
```

코드 해석:
- `IAttackable` 인터페이스 배열로 구현 클래스들을 공통 처리합니다.
- 호출 시 실제 객체(`Slime`, `Orc`)의 구현 메서드가 실행됩니다.
- 인터페이스를 사용하면 상속 구조와 별개로 공통 기능 계약을 설계할 수 있습니다.

연습문제:
1. 문제 1 - Skill 다형성
   - 문제: `ISkill` 인터페이스를 만들고 두 스킬 클래스를 구현하세요.
   - 입력: 없음
   - 출력: 스킬 사용 로그
   - 조건: 인터페이스 구현 클래스 2개 이상
2. 문제 2 - 컬렉션 순회
   - 문제: 인터페이스 타입 리스트에 구현 객체를 넣고 공통 메서드를 호출하세요.
   - 입력: 없음
   - 출력: 객체별 메시지
   - 힌트: `List<ISkill>`

정답 포인트:
- 다형성은 "확장에 열려 있고 수정에는 닫힌" 구조에 도움
- 기반 계약(추상 클래스/인터페이스)을 먼저 명확히 정의

---

[상위 문서로 돌아가기](./README.md)
