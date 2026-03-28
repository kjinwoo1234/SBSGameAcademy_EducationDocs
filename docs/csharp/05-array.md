# C# 배열

배열은 동일 타입 데이터를 고정 크기로 저장하는 자료구조입니다.  
반복문과 함께 가장 많이 사용되는 기본 컬렉션입니다.

## 1) 배열 선언/초기화
```csharp
int[] scores = new int[3];
scores[0] = 80;
scores[1] = 90;
scores[2] = 100;
```

## 2) 순회: `for`와 `foreach`
```csharp
int sum = 0;
foreach (int s in scores)
{
    sum += s;
}
double avg = (double)sum / scores.Length;
Console.WriteLine(avg);
```

## 3) 다차원 배열
```csharp
int[,] map = { {1, 2}, {3, 4} };
Console.WriteLine(map[1, 0]); // 3
```

## 4) 연습 문제
1. 배열 최댓값/최솟값 찾기
2. 점수 배열 평균 계산 프로그램 작성

---

[상위 문서로 돌아가기](./README.md)
