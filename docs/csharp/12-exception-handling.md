# C# 예외 처리

## 핵심
- `try`: 오류 가능 코드
- `catch`: 오류 발생 시 처리
- `finally`: 성공/실패와 무관하게 마무리

## 예제
```csharp
try {
    int x = 10;
    int y = 0;
    Console.WriteLine(x / y);
}
catch (Exception ex) { Console.WriteLine(ex.Message); }
finally { Console.WriteLine("종료"); }
```

---

[상위 문서로 돌아가기](./README.md)
