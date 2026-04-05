# Chapter 13 다형성

## 학습 목표
- 가상 함수 기반 다형성의 동작 원리를 이해한다.
- 기반 포인터로 파생 객체를 다루는 방법을 익힌다.

## 세부 주제
### 13-1 가상 함수(`virtual`)
- 런타임 바인딩

### 13-2 업캐스팅
- 기반 포인터로 파생 객체 참조

### 13-3 가상 소멸자
- 안전한 자원 정리

## 실습 체크리스트
- `Enemy` 기반 타입 배열에 여러 파생 객체를 담아 공통 인터페이스로 호출한다.

## 본문

### 13-1 가상 함수(`virtual`)
- 가상 함수는 실행 시점(런타임)에 실제 객체 타입을 보고 호출 함수를 결정합니다.
- 이를 통해 같은 함수 호출 문장으로도 객체별 다른 동작을 만들 수 있습니다.

### 13-2 업캐스팅
- 파생 객체 주소를 기반 클래스 포인터에 담는 것을 업캐스팅이라고 합니다.
- 업캐스팅 자체는 자동으로 가능하며, 공통 인터페이스 처리에 유용합니다.

### 13-3 가상 소멸자
- 기반 클래스를 포인터로 삭제할 수 있다면 소멸자는 `virtual`이어야 안전합니다.
- 가상 소멸자가 없으면 파생 클래스 자원 정리가 누락될 수 있습니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 다형성(polymorphism) | 같은 인터페이스 호출로 객체마다 다른 동작을 수행하는 특성 |
| 업캐스팅(upcasting) | 파생 타입을 기반 타입으로 다루는 변환 |

예시 코드:
```cpp
#include <iostream>
using namespace std;

class Enemy {
public:
    virtual ~Enemy() = default;
    virtual void attack() const { cout << "Enemy attack\n"; }
};

class Slime : public Enemy {
public:
    void attack() const override { cout << "Slime jump attack\n"; }
};

class Orc : public Enemy {
public:
    void attack() const override { cout << "Orc axe attack\n"; }
};

int main() {
    Enemy* e1 = new Slime();
    Enemy* e2 = new Orc();
    e1->attack();
    e2->attack();
    delete e1;
    delete e2;
    return 0;
}
```

예상 출력:
```text
Slime jump attack
Orc axe attack
```

코드 해석:
- `Enemy*` 타입으로 받았지만 `virtual` 덕분에 실제 객체 버전의 `attack()`이 호출됩니다.
- `override`는 "기반 가상 함수를 정확히 재정의"했는지 컴파일러가 확인하도록 돕습니다.
- `virtual ~Enemy()`가 있어 `delete` 시 파생 객체 소멸까지 안전하게 실행됩니다.

연습문제:
1. 문제 1 - 다형성 출력
   - 문제: `Weapon` 기반 클래스와 `Sword`, `Bow` 파생 클래스를 만들어 `use()`를 다형성으로 호출하세요.
   - 입력: 없음
   - 출력: 무기별 동작 로그
   - 조건: `virtual` + `override` 사용
2. 문제 2 - 소멸 로그 확인
   - 문제: 기반/파생 소멸자에 로그를 넣고 삭제 순서를 확인하세요.
   - 입력: 없음
   - 출력: 소멸 순서 로그
   - 힌트: 기반 소멸자는 반드시 `virtual`

정답 포인트:
- 다형성 핵심은 기반 인터페이스 + 가상 함수
- 포인터 삭제가 있는 기반 클래스에는 가상 소멸자 권장

---

[상위 문서로 돌아가기](./README.md)
