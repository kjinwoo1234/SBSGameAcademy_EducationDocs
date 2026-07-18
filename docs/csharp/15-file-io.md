# Chapter 15 파일 입출력

## 학습 목표
- 텍스트 파일 읽기/쓰기 기본을 익힌다.
- 파일 경로와 예외 처리의 기본 습관을 갖춘다.
- `File.Exists`로 존재 여부를 확인한 뒤 읽을 수 있다.

## 본문

### 15-1 파일 쓰기

텍스트를 파일로 남기려면 `System.IO`의 **`File.WriteAllText(경로, 내용)`**을 씁니다. 경로에 파일이 있으면 **내용을 덮어씁니다**. 경로 문자열은 변수로 분리해 두면 읽기·쓰기에서 같은 파일을 가리키기 쉽습니다.

### 15-2 파일 읽기

**`File.ReadAllText`**는 파일 전체를 하나의 `string`으로 돌려줍니다. **`File.ReadAllLines`**는 줄 단위 `string[]`입니다. 한 덩어리 설정 문자열이면 TextAllText, 줄마다 레코드면 ReadAllLines가 맞습니다.

| API | 결과 | 이럴 때 |
|---|---|---|
| `File.WriteAllText` | 파일에 문자열 저장(덮어쓰기) | 저장/세이브 |
| `File.ReadAllText` | 전체 `string` | 짧은 설정·한 덩어리 |
| `File.ReadAllLines` | `string[]` (줄 배열) | 줄 단위 데이터 |

### 15-3 파일 존재/예외 처리

경로가 틀리거나 권한이 없으면 예외가 납니다. 읽기 전에 **`File.Exists(path)`**로 존재를 확인하고, 필요하면 `try-catch`로 감쌉니다. **상대 경로**는 실행 위치를 기준으로, **절대 경로**는 드라이브부터 전체 경로입니다.

| 용어 | 의미 |
|---|---|
| 상대 경로 | 현재 실행 위치를 기준으로 해석되는 경로 |
| 절대 경로 | 드라이브/루트부터 시작하는 전체 경로 |

아래 코드를 실행해 `save.txt`에 쓴 뒤 같은 내용이 다시 출력되는지 확인해 보세요. (실행 폴더에 파일이 생깁니다.)

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "save.txt";
        File.WriteAllText(path, "Player=Kim,Level=5");

        if (File.Exists(path))
        {
            string text = File.ReadAllText(path);
            Console.WriteLine(text);
        }
        else
        {
            Console.WriteLine("파일 없음");
        }
    }
}
```

**예상 출력**

```text
Player=Kim,Level=5
```

**코드 해석**
- `WriteAllText`로 문자열을 저장합니다.
- `Exists`가 참이면 `ReadAllText`로 읽어 출력합니다.
- 쓰기와 읽기에 같은 `path`를 써서 한 흐름으로 검증합니다.

### 연습문제

**문제 1**
- 문제: 사용자 입력 문장 1줄을 파일에 저장하세요.
- 입력: 예: `hello save`
- 출력: `저장 완료`
- 조건: 경로를 변수로 두고 `WriteAllText` 사용

**문제 2**
- 문제: 저장한 파일을 다시 읽어 출력하세요.
- 입력: 없음(문제 1과 같은 경로)
- 출력: 파일 내용(예: `hello save`)
- 조건: 없으면 `파일 없음` 출력 (`Exists` 또는 `try-catch`)

### 정답 포인트

- 문제 1: `string path = "memo.txt"; File.WriteAllText(path, line);`
- 문제 2: `if (File.Exists(path)) Console.WriteLine(File.ReadAllText(path));`
- 공통: 경로·존재·권한을 먼저 생각하고, 저장 포맷을 정해 두기

---

[상위 문서로 돌아가기](./README.md)
