# C++ 동적 메모리

## 핵심
- `new`로 할당, `delete`로 해제
- 해제 후 `nullptr`로 초기화 권장

## 예제
```cpp
int* p = new int(5);
std::cout << *p << '\n';
delete p;
p = nullptr;
```

---

[상위 문서로 돌아가기](./README.md)
