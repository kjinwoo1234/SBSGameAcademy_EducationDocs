# [C++ 자습 자료] 02. 연산자와 제어문

## 1. 연산자
- 산술: `+`, `-`, `*`, `/`, `%`
- 비교: `==`, `!=`, `>`, `<`, `>=`, `<=`
- 논리: `&&`, `||`, `!`

```cpp
int n = 5;
int a = ++n; // 전위
int b = n++; // 후위
```

---

## 2. 조건문
```cpp
int score = 87;
if (score >= 90) {
    std::cout << "A\n";
} else if (score >= 80) {
    std::cout << "B\n";
} else {
    std::cout << "C\n";
}
```

---

## 3. 반복문
```cpp
for (int i = 1; i <= 5; i++) {
    std::cout << i << ' ';
}
```
