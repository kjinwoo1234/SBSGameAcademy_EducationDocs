# C# 파일 입출력

## 핵심
- 빠른 저장/읽기: `File.WriteAllText`, `File.ReadAllText`
- 줄 단위 처리: `StreamWriter`, `StreamReader`

## 예제
```csharp
File.WriteAllText("test.txt", "hello");
string text = File.ReadAllText("test.txt");
Console.WriteLine(text);
```

---

[상위 문서로 돌아가기](./README.md)
