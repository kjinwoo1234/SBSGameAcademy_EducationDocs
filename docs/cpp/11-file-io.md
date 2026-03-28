# C++ 파일 입출력

## 핵심
- 파일 쓰기: `std::ofstream`
- 파일 읽기: `std::ifstream`
- 한 줄 읽기: `std::getline`

## 예제
```cpp
std::ofstream out("log.txt");
out << "Hello File\n";
out.close();
```

---

[상위 문서로 돌아가기](./README.md)
