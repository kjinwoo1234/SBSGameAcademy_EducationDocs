# C++ 제어문

제어문은 프로그램 흐름을 설계하는 핵심 문법입니다.  
조건 분기와 반복 처리를 명확히 구분해 작성해야 버그를 줄일 수 있습니다.

## 1) 조건문
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

## 2) 선택문 `switch`
```cpp
int menu = 2;
switch (menu) {
    case 1: std::cout << "조회\n"; break;
    case 2: std::cout << "등록\n"; break;
    default: std::cout << "기타\n"; break;
}
```

## 3) 반복문/흐름 제어
```cpp
for (int i = 1; i <= 10; i++) {
    if (i == 3) continue;
    if (i == 8) break;
    std::cout << i << ' ';
}
```

## 4) 연습 문제
1. 1~100 합계를 반복문으로 계산하세요.
2. `switch`로 간단한 계산기 메뉴를 만드세요.

---

[상위 문서로 돌아가기](./README.md)
