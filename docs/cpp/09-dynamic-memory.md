# C++ 동적 메모리

동적 메모리는 실행 중 필요한 크기를 유연하게 확보할 수 있게 합니다.  
단, 수동 해제 실수로 메모리 누수/오류가 발생할 수 있으므로 관리 규칙이 중요합니다.

## 1) `new` / `delete`
```cpp
int* p = new int(5);
std::cout << *p << '\n';
delete p;
p = nullptr;
```

## 2) 동적 배열
```cpp
int n = 5;
int* arr = new int[n];
for (int i = 0; i < n; i++) arr[i] = i + 1;
delete[] arr;
arr = nullptr;
```

## 3) 메모리 실수 유형
- `delete` 누락 -> 메모리 누수
- `delete[]` 대신 `delete` 사용
- 해제 후 접근(Use-After-Free)

## 4) 현대 C++ 권장
가능하면 `std::vector`, `std::string`과 같은 RAII 컨테이너를 우선 사용하세요.

## 5) 연습 문제
1. 사용자 입력 크기의 동적 배열을 만들고 평균을 구하세요.
2. `new[]`/`delete[]` 짝을 정확히 맞춰 코드를 작성하세요.

---

[상위 문서로 돌아가기](./README.md)
