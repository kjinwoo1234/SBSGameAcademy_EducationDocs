# C# 예외 처리

예외 처리는 비정상 상황에서도 프로그램을 안전하게 유지하는 기술입니다.  
입력 오류, 파일 누락, 네트워크 오류 등 예측 가능한 실패를 관리해야 합니다.

## 1) 기본 구조
```csharp
try
{
    int x = 10;
    int y = 0;
    Console.WriteLine(x / y);
}
catch (DivideByZeroException ex)
{
    Console.WriteLine($"0으로 나눌 수 없습니다: {ex.Message}");
}
finally
{
    Console.WriteLine("정리 작업");
}
```

## 2) 예외 던지기
```csharp
if (age < 0)
    throw new ArgumentException("나이는 음수일 수 없습니다.");
```

## 3) 실무 원칙
- 가능한 구체 예외를 먼저 처리
- 사용자 메시지와 로그 메시지 분리
- `finally` 또는 `using`으로 자원 정리

## 4) 연습 문제
1. 숫자 입력 파싱 오류를 예외 처리하세요.
2. 파일 읽기 실패 시 안내 메시지를 출력하세요.

---

[상위 문서로 돌아가기](./README.md)
