# [C++ 자습 자료] 03. 배열, 문자열, 함수

## 1. 배열
```cpp
int nums[5] = {10, 20, 30, 40, 50};
for (int i = 0; i < 5; i++) {
    std::cout << nums[i] << ' ';
}
```

---

## 2. 문자열 (`std::string`)
```cpp
std::string name = "Kim";
std::cout << name.length() << '\n';
```

---

## 3. 함수와 참조 전달
- 값 전달: 원본 변경 없음
- 참조 전달: 원본 변경 가능

```cpp
void IncreaseByValue(int x) { x++; }
void IncreaseByRef(int& x) { x++; }
```
