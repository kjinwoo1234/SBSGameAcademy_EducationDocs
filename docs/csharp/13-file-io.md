# Chapter 13 파일 입출력

## 학습 목표
- 텍스트 파일 읽기/쓰기 기본을 익힌다.
- 파일 경로와 예외 처리의 기본 습관을 갖춘다.

## 세부 주제
### 13-1 파일 쓰기
- `File.WriteAllText`

### 13-2 파일 읽기
- `File.ReadAllText`, `ReadAllLines`

### 13-3 파일 존재/예외 처리
- `File.Exists`, `try-catch`

## 실습 체크리스트
- 간단한 저장/불러오기 기능을 구현한다.

## 본문

### 13-1 파일 쓰기
- `File.WriteAllText`로 텍스트를 한 번에 저장할 수 있습니다.
- 기존 파일이 있으면 덮어쓸 수 있으므로 주의가 필요합니다.

### 13-2 파일 읽기
- `ReadAllText`는 전체 문자열, `ReadAllLines`는 줄 배열을 반환합니다.
- 데이터 형식이 정해져 있으면 파싱 규칙을 함께 설계해야 합니다.

### 13-3 파일 존재/예외 처리
- 파일 경로 오류나 권한 문제는 예외로 이어질 수 있습니다.
- 저장/읽기 전에 존재 여부와 경로를 점검합니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 상대 경로 | 현재 실행 위치를 기준으로 해석되는 경로 |
| 절대 경로 | 드라이브/루트부터 시작하는 전체 경로 |

예시 코드:
```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "save.txt";
        File.WriteAllText(path, "Player=Kim,Level=5");
        string text = File.ReadAllText(path);
        Console.WriteLine(text);
    }
}
```

예상 출력:
```text
Player=Kim,Level=5
```

코드 해석:
- `WriteAllText`로 문자열을 파일에 저장하고 `ReadAllText`로 다시 읽습니다.
- 같은 경로를 사용해 저장/불러오기 흐름을 한 번에 검증합니다.

연습문제:
1. 문제 1 - 메모장 저장
   - 문제: 사용자 입력 문장을 파일에 저장하세요.
   - 입력: 문장 1줄
   - 출력: 저장 완료 메시지
   - 조건: 파일 경로 변수로 분리
2. 문제 2 - 파일 읽기
   - 문제: 저장한 파일을 다시 읽어 출력하세요.
   - 입력: 없음
   - 출력: 파일 내용
   - 힌트: 파일 없으면 예외 처리

정답 포인트:
- 경로/권한 문제를 항상 고려
- 파일 포맷 규칙을 먼저 정해두면 유지보수 용이

---

[상위 문서로 돌아가기](./README.md)
