# C++ 클래스

## 핵심
- 데이터와 기능을 하나로 캡슐화
- 접근 지정자: `public`, `private`, `protected`

## 예제
```cpp
class Player {
private:
    int hp;
public:
    Player(int value) : hp(value) {}
    int GetHp() const { return hp; }
};
```

---

[상위 문서로 돌아가기](./README.md)
