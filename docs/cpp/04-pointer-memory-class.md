# [C++ 자습 자료] 04. 포인터, 동적 메모리, 클래스

## 1. 포인터 기초
```cpp
int n = 10;
int* p = &n;
std::cout << *p << '\n';
```

---

## 2. 동적 메모리
`new/delete`를 짝으로 사용합니다.

```cpp
int* p = new int(5);
std::cout << *p << '\n';
delete p;
p = nullptr;
```

---

## 3. 클래스
캡슐화와 멤버 함수를 통해 데이터를 안전하게 관리합니다.

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
