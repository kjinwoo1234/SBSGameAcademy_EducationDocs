# Chapter 14 추상 클래스와 인터페이스 설계

## 학습 목표
- 순수 가상 함수를 이용한 추상 클래스 설계를 이해한다.
- 인터페이스 중심 설계의 장점을 설명할 수 있다.

## 세부 주제
### 14-1 순수 가상 함수
- `= 0` 문법

### 14-2 추상 클래스
- 객체 생성 불가 특성

### 14-3 인터페이스 설계
- 구현 분리와 확장성

## 실습 체크리스트
- `IRenderable` 인터페이스를 만들고 2개 이상의 구현 클래스를 작성한다.

## 본문

### 14-1 순수 가상 함수
- `virtual void f() = 0;` 형태를 순수 가상 함수라고 합니다.
- 파생 클래스가 반드시 구현해야 하는 계약(약속)을 제공합니다.

### 14-2 추상 클래스
- 순수 가상 함수를 1개 이상 가진 클래스는 추상 클래스가 됩니다.
- 추상 클래스는 직접 객체를 만들 수 없고, 파생 클래스로만 사용합니다.

### 14-3 인터페이스 설계
- 공통 기능 약속만 담고 구현은 파생 클래스에 맡기면 결합도를 낮출 수 있습니다.
- 새로운 타입을 추가할 때 기존 호출 코드를 거의 수정하지 않아도 됩니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 순수 가상 함수 | 파생 클래스에서 구현을 강제하는 가상 함수 |
| 인터페이스(interface) | 외부에 제공할 기능 계약만 정의한 추상화 계층 |

예시 코드:
```cpp
#include <iostream>
using namespace std;

class IRenderable {
public:
    virtual ~IRenderable() = default;
    virtual void render() const = 0;
};

class PlayerSprite : public IRenderable {
public:
    void render() const override { cout << "Render Player\n"; }
};

class EnemySprite : public IRenderable {
public:
    void render() const override { cout << "Render Enemy\n"; }
};

int main() {
    IRenderable* arr[2];
    PlayerSprite p;
    EnemySprite e;
    arr[0] = &p;
    arr[1] = &e;
    for (int i = 0; i < 2; i++) arr[i]->render();
    return 0;
}
```

예상 출력:
```text
Render Player
Render Enemy
```

코드 해석:
- `IRenderable`은 "render 기능 약속"만 제공하는 추상 클래스입니다.
- 각 파생 클래스가 `render()` 구현을 맡아 책임을 분리합니다.
- 호출 측은 `IRenderable*`만 알면 되므로 구체 타입에 덜 의존합니다.

연습문제:
1. 문제 1 - 인터페이스 구현
   - 문제: `IMovable` 인터페이스를 만들고 `Player`, `Monster`에 구현하세요.
   - 입력: 이동 거리
   - 출력: 이동 로그
   - 조건: 순수 가상 함수 1개 이상
2. 문제 2 - 공통 처리 함수
   - 문제: 인터페이스 포인터 배열을 받아 모두 호출하는 함수를 작성하세요.
   - 입력: 객체 배열
   - 출력: 객체별 실행 로그
   - 힌트: 기반 타입 포인터 활용

정답 포인트:
- 추상 클래스는 직접 생성 불가
- 인터페이스 중심 설계는 확장성과 테스트 용이성에 유리

---

[상위 문서로 돌아가기](./README.md)
