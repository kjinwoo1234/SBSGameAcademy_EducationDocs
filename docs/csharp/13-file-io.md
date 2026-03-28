# C# 파일 입출력

파일 입출력은 데이터를 영구 저장하기 위한 필수 기능입니다.  
간단한 텍스트 저장부터 로그 기록, 데이터 백업까지 다양한 용도로 사용됩니다.

## 1) 빠른 텍스트 저장/읽기
```csharp
using System.IO;

File.WriteAllText("test.txt", "hello");
string text = File.ReadAllText("test.txt");
Console.WriteLine(text);
```

## 2) 줄 단위 처리
```csharp
using (StreamWriter sw = new StreamWriter("log.txt"))
{
    sw.WriteLine("line1");
    sw.WriteLine("line2");
}

using (StreamReader sr = new StreamReader("log.txt"))
{
    while (!sr.EndOfStream)
        Console.WriteLine(sr.ReadLine());
}
```

## 3) 파일 존재 확인
```csharp
if (File.Exists("test.txt"))
    Console.WriteLine("파일 존재");
```

## 4) 연습 문제
1. 사용자 입력을 파일에 저장하고 재실행 시 읽기
2. 날짜/시간 로그를 줄 단위로 누적 저장

---

[상위 문서로 돌아가기](./README.md)
