# Chapter 23 Null 안전성

## 학습 목표
- `null` 관련 런타임 오류가 왜 발생하는지 이해한다.
- nullable reference types와 null 안전 연산자를 적용할 수 있다.

## 세부 주제
### 23-1 null과 예외
- `NullReferenceException` 발생 원인

### 23-2 nullable reference types
- `string` vs `string?`, `#nullable`

### 23-3 null 안전 연산자
- `?.`, `??`, `??=`, null 체크 패턴

## 실습 체크리스트
- null 가능한 입력/조회 흐름에 안전 연산자를 적용한다.

## 본문

### 23-1 null과 예외
- 참조형 변수가 `null`일 때 멤버에 접근하면 `NullReferenceException`이 발생할 수 있습니다.
- null 안전성은 이런 오류를 "실행 전에" 최대한 발견하고 방지하는 개념입니다.

### 23-2 nullable reference types
- `string`은 null 불가, `string?`는 null 가능으로 의도를 명시합니다.
- `#nullable enable`을 사용하면 컴파일러가 null 위험 코드를 경고해줍니다.

### 23-3 null 안전 연산자
- `obj?.Name`은 `obj`가 null이면 예외 대신 null을 반환합니다.
- `name ?? "Guest"`는 null일 때 기본값을 제공합니다.
- `value ??= "default"`는 null인 경우에만 값을 대입합니다.

약어 설명(NRT):
- **NRT**는 **Nullable Reference Types**의 약자입니다.
- 한국어로는 "널 가능 참조 형식"이며, 참조형의 null 허용 여부를 타입 수준에서 명시하는 기능입니다.
- null 관련 버그를 컴파일 단계에서 줄이는 것이 핵심 목적입니다.

연산자 비교:
| 표현 | 의미 |
|---|---|
| `?.` | null이면 접근 중단 후 null 반환 |
| `??` | 좌변이 null일 때 우변 기본값 사용 |
| `??=` | 좌변이 null일 때만 대입 |

예시 코드:
```csharp
#nullable enable
using System;

class User
{
    public string? Nickname { get; set; }
}

class Program
{
    static void Main()
    {
        User? user = null;
        string displayName = user?.Nickname ?? "Guest";
        Console.WriteLine(displayName);
    }
}
```

예상 출력:
```text
Guest
```

코드 해석:
- `user?.Nickname`에서 `user`가 null이면 예외를 내지 않고 null을 반환합니다.
- 이어서 `?? "Guest"`가 동작해 안전한 기본값을 출력합니다.

연습문제:
1. 문제 1 - 안전한 프로필 출력
   - 문제: 사용자 프로필 객체가 null일 때도 예외 없이 이름을 출력하세요.
   - 입력: null 또는 사용자 객체
   - 출력: 이름 또는 기본 문자열
   - 조건: `?.`, `??` 사용
2. 문제 2 - 초기값 보정
   - 문제: 설정 문자열이 null이면 기본 경로를 할당하세요.
   - 입력: `string? path`
   - 출력: 보정된 path
   - 힌트: `??=` 사용
3. 문제 3 - NRT 경고 점검
   - 문제: `#nullable enable` 상태에서 경고를 발생시키고 수정하세요.
   - 입력: null 가능 참조 코드
   - 출력: 경고 제거 코드
   - 조건: 경고 원인 설명 1줄 포함

정답 포인트:
- null 안전성은 예외 회피가 아니라 설계 의도 명확화
- NRT + 안전 연산자 조합이 실무 기본 패턴

---

[상위 문서로 돌아가기](./README.md)
