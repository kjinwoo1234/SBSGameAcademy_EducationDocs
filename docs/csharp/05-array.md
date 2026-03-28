# C# 배열

## 핵심
- 배열은 같은 자료형 데이터를 고정 크기로 저장
- 인덱스는 0부터 시작
- 길이는 `Length`

## 예제
```csharp
int[] scores = { 80, 90, 100 };
int sum = 0;
foreach (int s in scores) sum += s;
Console.WriteLine((double)sum / scores.Length);
```

---

[상위 문서로 돌아가기](./README.md)
