# C# 프로퍼티

## 핵심
- 필드를 안전하게 감싸는 접근 통로
- `get`, `set`에서 유효성 검사 가능
- 자동 구현 프로퍼티 사용 가능

## 예제
```csharp
public int Age
{
    get => age;
    set => age = value < 0 ? 0 : value;
}
```

---

[상위 문서로 돌아가기](./README.md)
