# Chapter 12 상속

## 학습 목표
- 상속의 목적과 기본 문법을 이해한다.
- 기반 클래스와 파생 클래스의 관계를 설명할 수 있다.

## 세부 주제
### 12-1 상속 기본 문법
- `class Derived : public Base`

### 12-2 생성자 호출 순서
- 기반 -> 파생 순서

### 12-3 접근 지정자와 상속
- `public` 상속 의미

## 실습 체크리스트
- `Character` 기반 클래스를 만들고 `Warrior` 파생 클래스를 구현한다.

## 본문

### 12-1 상속 기본 문법
- 상속은 기존 클래스의 데이터/기능을 재사용해 새 클래스를 만드는 방법입니다.
- 공통 기능은 기반 클래스에 두고, 차이점은 파생 클래스에 추가합니다.

### 12-2 생성자 호출 순서
- 객체 생성 시 기반 클래스 생성자가 먼저 호출되고, 그 다음 파생 클래스 생성자가 호출됩니다.
- 소멸은 반대로 파생 -> 기반 순서로 진행됩니다.

### 12-3 접근 지정자와 상속
- `public` 상속에서는 기반 클래스의 `public` 멤버가 파생 클래스에서도 `public`으로 유지됩니다.
- `private` 멤버는 파생 클래스에서 직접 접근할 수 없습니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 기반 클래스(Base) | 공통 기능을 제공하는 부모 클래스 |
| 파생 클래스(Derived) | 기반 클래스를 확장한 자식 클래스 |

예시 코드:
```cpp
#include <iostream>
using namespace std;

class Character {
public:
    Character(int hp) : hp_(hp) {}
    int getHp() const { return hp_; }
protected:
    int hp_;
};

class Warrior : public Character {
public:
    Warrior(int hp, int atk) : Character(hp), atk_(atk) {}
    int getAtk() const { return atk_; }
private:
    int atk_;
};

int main() {
    Warrior w(120, 30);
    cout << "hp: " << w.getHp() << ", atk: " << w.getAtk() << endl;
    return 0;
}
```

예상 출력:
```text
hp: 120, atk: 30
```

코드 해석:
- `Warrior : public Character`는 `Character`를 기반 클래스로 상속한다는 의미입니다.
- `Warrior` 생성자에서 `Character(hp)`를 먼저 호출해 기반 부분을 초기화합니다.
- 파생 클래스는 기반 클래스의 공개 인터페이스(`getHp`)를 그대로 사용할 수 있습니다.

연습문제:
1. 문제 1 - 상속 구조 만들기
   - 문제: `Animal` 기반 클래스와 `Dog` 파생 클래스를 만들고 정보를 출력하세요.
   - 입력: 이름, 나이
   - 출력: 동물 정보
   - 조건: 기반 클래스 멤버 함수 재사용
2. 문제 2 - 생성자 순서 확인
   - 문제: 생성자에 로그를 넣어 호출 순서를 확인하세요.
   - 입력: 없음
   - 출력: 생성 순서 로그
   - 힌트: 기반/파생 생성자에 `cout` 추가

정답 포인트:
- 공통 속성/기능은 기반 클래스로 분리
- 생성자 호출 순서(기반 -> 파생) 기억

---

[상위 문서로 돌아가기](./README.md)
