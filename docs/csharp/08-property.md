# Chapter 08 프로퍼티

## 학습 목표
- 프로퍼티의 목적과 문법을 이해한다.
- 필드 직접 노출 대신 프로퍼티를 사용하는 이유를 설명할 수 있다.

## 세부 주제
### 08-1 프로퍼티 기본
- `get`, `set`

### 08-2 자동 구현 프로퍼티
- 축약 문법

### 08-3 접근 제어
- `private set`

## 실습 체크리스트
- `Player` 클래스에 읽기 전용/쓰기 가능 프로퍼티를 섞어 설계한다.

## 본문

### 08-1 프로퍼티 기본
- 프로퍼티는 필드 접근에 규칙을 추가하는 안전한 창구입니다.
- 외부에서는 변수처럼 보이지만 내부에서는 메서드처럼 동작합니다.

### 08-2 자동 구현 프로퍼티
- `public int Level { get; set; }`처럼 간단히 선언할 수 있습니다.
- 백킹 필드를 직접 쓰지 않아도 됩니다.

### 08-3 접근 제어
- `public int Hp { get; private set; }`처럼 setter 접근 제한이 가능합니다.
- 읽기는 허용하고 수정은 클래스 내부만 허용하는 설계에 유용합니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 프로퍼티(property) | 필드 접근을 제어하는 C# 문법 |
| 백킹 필드(backing field) | 프로퍼티가 내부에서 값을 저장하는 실제 필드 |

예시 코드:
```csharp
using System;

class Player
{
    public string Name { get; set; } = "";
    public int Hp { get; private set; } = 100;
    public void Damage(int d) => Hp -= d;
}

class Program
{
    static void Main()
    {
        Player p = new Player { Name = "Kim" };
        p.Damage(20);
        Console.WriteLine($"{p.Name} {p.Hp}");
    }
}
```

예상 출력:
```text
Kim 80
```

코드 해석:
- `Name`은 읽기/쓰기 모두 가능하고 `Hp`는 `private set`으로 외부 수정을 막습니다.
- `Damage` 메서드만 체력 변경 경로로 두어 상태 변경 규칙을 통제합니다.

연습문제:
1. 문제 1 - 읽기 전용 프로퍼티
   - 문제: 생성 시만 설정 가능한 `Id` 프로퍼티를 설계하세요.
   - 입력: Id 값
   - 출력: Id 출력
   - 조건: 외부 수정 불가
2. 문제 2 - 유효성 검사
   - 문제: `Level` 프로퍼티를 1 이상만 허용하도록 구현하세요.
   - 입력: 레벨 값
   - 출력: 저장된 레벨
   - 힌트: `set` 블록 조건문

정답 포인트:
- 필드 공개보다 프로퍼티 공개가 기본
- 쓰기 제한(`private set`)으로 안정성 향상

---

[상위 문서로 돌아가기](./README.md)
