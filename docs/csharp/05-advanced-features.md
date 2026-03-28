# [C# 자습 자료] 08. 제네릭 (Generics)

데이터 형식을 미리 정하지 않고, 사용할 때 결정하도록 만들어 코드의 재사용성을 높이는 기술입니다.

## 1. 제네릭의 필요성
만약 정수 전용 리스트와 문자열 전용 리스트를 각각 만든다면 코드가 낭비됩니다. 제네릭을 쓰면 하나의 설계도로 모든 타입을 다룰 수 있습니다.

## 2. 제네릭 컬렉션 - List<T>
배열과 비슷하지만 크기가 자유롭게 변하는 가장 많이 쓰이는 자료구조입니다. `<T>` 자리에 원하는 자료형을 넣습니다.

[예제 코드]
```
// 정수형 리스트
List<int> numbers = new List<int>();
numbers.Add(10);
numbers.Add(20);

// 문자열 리스트
List<string> names = new List<string>();
names.Add("홍길동");
names.Add("이순신");

foreach (string n in names)
{
    Console.WriteLine($"명단: {n}");
}
```

---

## 3. 제네릭 메서드 만들기
메서드 이름 뒤에 `<T>`를 붙여서 어떤 타입이든 받을 수 있게 만듭니다.

[예제 코드]
```
static void PrintAnything<T>(T value)
{
    Console.WriteLine($"홍길동의 데이터: {value}");
}

// 사용 시점에 타입이 결정됨
PrintAnything<int>(123);
PrintAnything<string>("안녕하세요");
```

# [C# 자습 자료] 09. 람다식과 LINQ (데이터 처리의 혁신)

복잡한 반복문을 한 줄로 줄여주고, 데이터를 SQL처럼 쉽고 직관적으로 추출하는 방법을 학습합니다.

---

## 1. 람다식 (Lambda Expression)
익명 함수(이름이 없는 함수)를 아주 간결하게 표현하는 방식입니다. `=>` 기호를 사용하며 "입력이 전달되면(input) 실행한다(output)"는 의미입니다.

[예제 코드]
```
// 원래 방식 (메서드 따로 정의)
static bool IsEven(int n) => n % 2 == 0;

// 람다식 방식 (필요할 때 즉석에서 정의)
// n이 입력되면 n % 2 == 0 인지 확인하라
(n => n % 2 == 0)
```

---

## 2. LINQ (Language Integrated Query)
컬렉션(배열, 리스트 등)의 데이터를 필터링, 정렬, 가공할 때 사용하는 강력한 기술입니다.

**주요 메서드:**
- **Where**: 조건에 맞는 데이터만 골라내기
- **OrderBy**: 정렬하기 (내림차순은 OrderByDescending)
- **Select**: 데이터 형태를 변형하거나 특정 부분만 추출하기
- **ToList / ToArray**: 결과를 리스트나 배열로 변환하기

[예제 코드]
```
using System.Linq;

int[] scores = { 95, 70, 45, 80, 100, 60 };

// 홍길동의 점수 중 80점 이상만 골라 큰 순서대로 정렬
var highScores = scores.Where(s => s >= 80)
                       .OrderByDescending(s => s)
                       .ToList();

foreach (int s in highScores)
{
    Console.WriteLine($"우수 점수: {s}");
}
```

---

## 3. LINQ 실무 활용 (객체 리스트 처리)
객체들이 담긴 리스트에서 특정 조건의 데이터만 뽑아내는 실무적인 예제입니다.

[예제 코드]
```
class Student { public string Name; public int Age; }

List<Student> students = new List<Student>
{
    new Student { Name = "홍길동", Age = 20 },
    new Student { Name = "이순신", Age = 30 },
    new Student { Name = "강감찬", Age = 15 }
};

// 성인(20세 이상)인 학생의 이름만 추출
var adultNames = students.Where(s => s.Age >= 20)
                         .Select(s => s.Name);

foreach (string name in adultNames)
{
    Console.WriteLine($"성인 명단: {name}");
}
```

---

# [C# 자습 자료] 10. 비동기 프로그래밍 (Async / Await)

프로그램이 오래 걸리는 작업을 할 때 화면이 멈추지 않게 하거나, 여러 작업을 효율적으로 동시에 처리하는 방법입니다.

## 1. 비동기의 개념
- **동기(Sync)**: 작업이 끝날 때까지 기다림 (커피가 나올 때까지 줄 서서 기다리기)
- **비동기(Async)**: 작업을 맡겨놓고 다른 일을 함 (진동벨을 받고 자리에 앉아 있기)

---

## 2. async와 await 사용법
- **async**: 이 메서드는 비동기로 동작한다는 것을 알립니다. 반환 타입은 `Task` 또는 `Task<T>`를 사용합니다.
- **await**: 비동기 작업이 끝날 때까지 기다리되, 그동안 프로그램의 다른 부분은 계속 돌아가게 합니다.

[예제 코드]
```
using System.Threading.Tasks;

// 데이터를 다운로드하는 척 하는 비동기 메서드
static async Task DownloadFileAsync()
{
    Console.WriteLine("홍길동님이 다운로드를 시작합니다.");
    
    // 3초 동안 비동기로 대기 (프로그램은 멈추지 않음)
    await Task.Delay(3000); 
    
    Console.WriteLine("다운로드가 완료되었습니다.");
}

// 메인 실행부에서 호출
await DownloadFileAsync();
Console.WriteLine("다른 작업을 수행 중입니다...");
```

---

## 3. 왜 비동기를 쓰는가?
파일 읽기/쓰기, 네트워크 통신, 데이터베이스 조회 등 시간이 걸리는 작업에서 **사용자 경험(UI 멈춤 방지)**과 **서버 자원 효율**을 극대화하기 위해 사용합니다.

# [C# 자습 자료] 11. 메모리 관리 (Value vs Reference & GC)

프로그램이 데이터를 메모리의 어디에 저장하고, 어떻게 치우는지 이해하면 효율적인 코드를 짤 수 있습니다.

---

## 1. 값 타입(Value Type) vs 참조 타입(Reference Type)
데이터가 메모리의 어느 구역에 저장되느냐에 따라 나뉩니다.

- **값 타입 (Stack 저장)**: 변수에 실제 값이 직접 들어감. 함수가 끝나면 바로 사라짐. (int, double, bool, struct 등)
- **참조 타입 (Heap 저장)**: 변수에는 데이터가 있는 '주소'만 저장됨. 실제 데이터는 Heap에 보관됨. (class, string, 배열 등)

[예제 코드]
```
int a = 10;           // Stack에 10 저장
string b = "홍길동";   // Stack에는 주소, Heap에는 "홍길동" 저장
```

---

## 2. 가비지 컬렉션 (Garbage Collection, GC)
C#은 개발자가 직접 메모리를 지울 필요가 없습니다. **가비지 컬렉터**가 주기적으로 Heap을 검사해서 아무도 사용하지 않는(참조가 끊긴) 데이터를 자동으로 치워줍니다.

---

## 3. using 문과 자원 해제 (IDisposable)
파일, 네트워크 연결, 데이터베이스 접속 등은 GC가 즉시 치우지 못할 때가 많습니다. 이때 `using` 문을 쓰면 작업이 끝나는 즉시 자원을 안전하게 반납합니다.

[예제 코드]
```
using (var reader = new StreamReader("test.txt"))
{
    string content = reader.ReadToEnd();
    Console.WriteLine($"홍길동의 파일 내용: {content}");
} // 여기서 reader 상자가 자동으로 닫히고 정리됨
```

---

# [C# 자습 자료] 12. 델리게이트와 이벤트 (Delegate & Event)

메서드를 변수에 담아 전달하거나, 특정 상황(클릭 등)이 발생했을 때 알려주는 기술입니다.

## 1. 델리게이트 (Delegate)
'대리자'라는 뜻으로, 메서드 자체를 변수에 담아서 다른 곳으로 전달할 수 있게 해줍니다.

[예제 코드]
```
// 1. 델리게이트 정의 (어떤 형태의 함수를 담을지 결정)
delegate void MyDelegate(string message);

static void ShowMessage(string msg) => Console.WriteLine($"홍길동의 알림: {msg}");

// 2. 메서드를 변수처럼 담아서 사용
MyDelegate del = ShowMessage;
del("안녕하세요!");
```

---

## 2. Func와 Action
델리게이트를 매번 정의하기 귀찮아서 만들어둔 표준 델리게이트입니다.
- **Action**: 반환값이 없는 메서드용
- **Func**: 반환값이 있는 메서드용

[예제 코드]
```
// Action: 입력은 string, 반환은 없음
Action<string> sayHello = (name) => Console.WriteLine($"안녕, {name}!");
sayHello("홍길동");

// Func: 입력은 int 2개, 반환은 int
Func<int, int, int> add = (x, y) => x + y;
int result = add(10, 20);
```

---

## 3. 이벤트 (Event)
특정 사건(버튼 클릭, 데이터 도착 등)이 일어났을 때 등록된 함수들에게 알려주는 시스템입니다. 델리게이트를 기반으로 동작하지만 더 안전합니다.

[예제 코드]
```
class Alarm
{
    public event Action OnAlarm; // 알람 이벤트

    public void Start()
    {
        Console.WriteLine("알람이 울립니다!");
        OnAlarm?.Invoke(); // 등록된 사람들에게 알림
    }
}

// 사용 예시
Alarm myAlarm = new Alarm();
myAlarm.OnAlarm += () => Console.WriteLine("홍길동이 일어났습니다.");
myAlarm.Start();
```
