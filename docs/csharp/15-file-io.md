# Chapter 15 파일 입출력

## 학습 목표
- `File.WriteAllText`로 텍스트를 저장할 수 있다.
- `File.ReadAllText`·`File.ReadAllLines`로 파일을 읽을 수 있다.
- `File.Exists`와 `try-catch`로 없는 파일·실패를 처리할 수 있다.

## 본문

### 15-1 파일 쓰기

텍스트를 파일로 남기려면 `System.IO`의 **`File.WriteAllText(경로, 내용)`**을 씁니다. 첫 인자는 파일 경로 문자열이고, 둘째 인자는 저장할 내용입니다. 경로에 파일이 있으면 **내용을 덮어씁니다**. 경로 문자열은 변수로 분리해 두면 읽기·쓰기에서 같은 파일을 가리키기 쉽습니다.

아래를 `Main`에 넣고 실행해, 저장 후 안내 문구가 나오는지 확인해 보세요. 실행한 폴더에 `save.txt` 파일이 생깁니다.

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "save.txt";
        File.WriteAllText(path, "Player=Kim,Level=5");
        Console.WriteLine("저장 완료");
    }
}
```

**예상 출력**

```text
저장 완료
```

**코드 해석**
- `path`에 파일 이름 `"save.txt"`를 둡니다.
- `WriteAllText`로 문자열 전체를 파일에 씁니다.
- 콘솔에는 저장이 끝났다는 메시지만 출력합니다.

### 15-2 파일 읽기

**`File.ReadAllText(경로)`**는 파일 전체를 하나의 `string`으로 돌려줍니다. **`File.ReadAllLines(경로)`**는 줄 단위 `string[]`입니다. 한 덩어리 설정 문자열이면 ReadAllText, 줄마다 레코드면 ReadAllLines가 맞습니다.

| API | 결과 | 이럴 때 |
|---|---|---|
| `File.WriteAllText` | 파일에 문자열 저장(덮어쓰기) | 저장/세이브 |
| `File.ReadAllText` | 전체 `string` | 짧은 설정·한 덩어리 |
| `File.ReadAllLines` | `string[]` (줄 배열) | 줄 단위 데이터 |

먼저 한 덩어리로 읽는 예제입니다. `15-1`에서 만든 `save.txt`가 있다고 가정하고, 아래를 실행해 같은 내용이 출력되는지 확인해 보세요.

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "save.txt";
        string text = File.ReadAllText(path);
        Console.WriteLine(text);
    }
}
```

**예상 출력**

```text
Player=Kim,Level=5
```

**코드 해석**
- `ReadAllText`로 파일 전체를 읽어 `text`에 담습니다.
- `Console.WriteLine(text)`로 저장했던 문자열을 그대로 출력합니다.

이어서 줄 단위 읽기입니다. 여러 줄이 있는 파일을 만든 뒤 `ReadAllLines`와 `foreach`로 한 줄씩 출력해 보세요.

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "lines.txt";
        File.WriteAllText(path, "apple\nbanana\ncherry");
        string[] lines = File.ReadAllLines(path);
        foreach (string line in lines)
        {
            Console.WriteLine(line);
        }
    }
}
```

**예상 출력**

```text
apple
banana
cherry
```

**코드 해석**
- `\n`으로 줄바꿈을 넣어 세 줄을 저장합니다.
- `ReadAllLines`는 줄마다 칸이 있는 배열을 돌려줍니다.
- `foreach`로 각 줄을 한 줄씩 출력합니다.

### 15-3 파일 존재와 예외 처리

경로가 틀리거나 파일이 없으면 읽기에서 예외가 납니다. 읽기 전에 **`File.Exists(path)`**로 존재를 확인하는 습관을 들입니다. 그래도 실패할 수 있으면 앞 장에서 배운 **`try-catch`**로 감싸 메시지를 보여 줍니다. **상대 경로**는 실행 위치를 기준으로, **절대 경로**는 드라이브부터 전체 경로입니다.

| 용어 | 의미 |
|---|---|
| 상대 경로 | 현재 실행 위치를 기준으로 해석되는 경로 |
| 절대 경로 | 드라이브부터 시작하는 전체 경로 |

아래를 실행해, 없는 파일을 읽을 때 Exists로 걸러지고 `파일 없음`이 나오는지 확인해 보세요.

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "missing.txt";
        try
        {
            if (File.Exists(path))
            {
                Console.WriteLine(File.ReadAllText(path));
            }
            else
            {
                Console.WriteLine("파일 없음");
            }
        }
        catch (IOException)
        {
            Console.WriteLine("읽기 실패");
        }
    }
}
```

**예상 출력**

```text
파일 없음
```

**코드 해석**
- `Exists`가 거짓이면 읽지 않고 `파일 없음`을 출력합니다.
- 존재해도 권한·잠금 등으로 실패할 수 있어 `try-catch`로 `IOException`을 받습니다.
- 이 예제에서는 파일이 없으므로 catch까지 가지 않고 else만 실행됩니다.

### 연습문제

**문제 1**
- 문제: 사용자 입력 문장 1줄을 파일에 저장하세요.
- 입력: 예 `hello save`
- 출력: `저장 완료`
- 조건: 경로를 변수로 두고 `WriteAllText` 사용

**문제 2**
- 문제: 저장한 파일을 다시 읽어 출력하세요.
- 입력: 없음. 문제 1과 같은 경로
- 출력: 파일 내용. 예 `hello save`
- 조건: 없으면 `파일 없음` 출력. `Exists` 또는 `try-catch` 사용

**문제 3**
- 문제: 두 줄짜리 파일을 만들고 줄마다 출력하세요.
- 입력: 없음. 내용 예 `red`와 `blue` 두 줄
- 출력:
  ```text
  red
  blue
  ```
- 조건: `WriteAllText`로 저장한 뒤 `ReadAllLines`와 반복문으로 출력

### 정답 포인트

- 문제 1: `string path = "memo.txt"; File.WriteAllText(path, line); Console.WriteLine("저장 완료");`
- 문제 2: `if (File.Exists(path)) Console.WriteLine(File.ReadAllText(path)); else Console.WriteLine("파일 없음");`
- 문제 3: `WriteAllText`에 `\n`으로 두 줄 → `ReadAllLines` → `foreach` 또는 `for`
- 공통: 경로를 변수로 두고, 읽기 전 존재·예외를 먼저 생각합니다.

---

[상위 문서로 돌아가기](./README.md)
