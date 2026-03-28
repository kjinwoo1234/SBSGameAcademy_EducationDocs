# C++ 클래스

클래스는 데이터(멤버 변수)와 기능(멤버 함수)을 하나로 묶는 객체지향 핵심 문법입니다.  
캡슐화를 통해 잘못된 상태 변경을 막고 유지보수를 쉽게 만듭니다.

## 1) 기본 구조
```cpp
class Player {
private:
    int hp;
public:
    Player(int value) : hp(value) {}
    void Hit(int damage) { hp -= damage; }
    int GetHp() const { return hp; }
};
```

## 2) 접근 지정자
- `private`: 클래스 내부에서만 접근
- `public`: 외부 접근 허용
- `protected`: 상속 관계에서 접근 가능

## 3) 생성자와 초기화 리스트
생성자 초기화 리스트는 멤버 초기화의 권장 방식입니다.

## 4) `const` 멤버 함수
객체 상태를 바꾸지 않는 함수임을 보장합니다.

## 5) 연습 문제
1. `Student` 클래스를 만들고 이름/점수/평균 출력 함수를 구현하세요.
2. 점수 입력 유효성 검사(0~100) 로직을 멤버 함수에 넣으세요.

---

[상위 문서로 돌아가기](./README.md)
