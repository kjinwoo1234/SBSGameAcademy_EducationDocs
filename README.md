# [C# 자습 자료] 01. 입출력과 변수/자료형

C# 프로그래밍의 가장 기초인 데이터를 입력받고 화면에 출력하는 방법, 그리고 데이터를 저장하는 상자인 변수에 대해 학습합니다.

---

## 1. 콘솔 출력 (화면에 데이터 보여주기)
프로그램이 처리한 결과를 사용자에게 보여줄 때 사용합니다.

- Console.WriteLine() : 내용을 출력한 뒤 자동으로 줄을 바꿉니다.
- Console.Write() : 내용을 출력만 하고 줄을 바꾸지 않습니다.

[예제 코드]
```
Console.WriteLine("이름: 홍길동");
Console.Write("나이: ");
Console.Write(20);
// 출력 결과:
// 이름: 홍길동
// 나이: 20
```

---

## 2. 변수(Variable)와 자료형(Data Types)
데이터를 저장하는 상자를 '변수'라고 하며, 상자의 종류를 '자료형'이라고 합니다.

- int : 정수 (예: 10, 0, -50)
- float : 실수 (예: 3.14f, 1.5f) - 뒤에 f는 실수라는 뜻
- string : 문자열 (예: "홍길동", "서울") - 큰따옴표 사용
- bool : 논리값 (true 또는 false)

[예제 코드]
```
string name = "홍길동";
int age = 20;
float height = 175.5f;
bool isStudent = true;
```

---

## 3. 사용자 입력 (데이터 받아오기)
사용자가 키보드로 입력한 내용을 프로그램으로 가져올 때 사용합니다.
*중요: 모든 입력값은 기본적으로 '문자열(string)'로 인식됩니다.*

[예제 코드]
```
Console.Write("이름을 입력하세요: ");
string inputName = Console.ReadLine(); // 사용자가 입력한 값을 inputName에 저장
Console.WriteLine(inputName + "님, 환영합니다!");
```

---

## 4. 문자열 보간 (변수와 문자 섞기)
문자열 앞에 $를 붙이면 { } 안에 변수 이름을 넣어 아주 쉽게 출력할 수 있습니다.

[예제 코드]
```
string name = "홍길동";
int age = 20;

// 가장 권장되는 방식
Console.WriteLine($"이름은 {name}이고 나이는 {age}세입니다.");
```

---

## 5. 형 변환 (문자열을 숫자로 바꾸기)
입력받은 문자열을 계산에 사용하려면 숫자로 변환하는 과정이 필요합니다.

[예제 코드]
```
Console.Write("태어난 연도를 입력하세요: ");
string inputYear = Console.ReadLine();

// 문자열을 정수(int)로 변환
int birthYear = int.Parse(inputYear); 
int currentYear = 2024;
int age = currentYear - birthYear + 1;

Console.WriteLine($"홍길동님의 한국 나이는 {age}세입니다.");
```
# [C# 자습 자료] 01-2. 연산자 (Operators)

데이터를 계산하거나 비교하고, 논리적으로 판단할 때 사용하는 기호들입니다.

---

## 1. 산술 연산자 (사칙연산)
숫자 데이터를 계산할 때 사용합니다.

- `+` : 더하기
- `-` : 빼기
- `*` : 곱하기
- `/` : 나누기 (정수끼리 나누면 결과도 정수, 실수로 얻으려면 하나는 실수여야 함)
- `%` : 나머지 (나눗셈 후 남는 값)

[예제 코드]
```
int a = 10;
int b = 3;

Console.WriteLine(a + b); // 13
Console.WriteLine(a / b); // 3 (몫)
Console.WriteLine(a % b); // 1 (나머지)
```

---

## 2. 증감 연산자
값을 1씩 증가시키거나 감소시킵니다.

- ++ : 값을 1 증가 (a = a + 1)
- -- : 값을 1 감소 (a = a - 1)

[예제 코드]
```
int count = 0;
count++; // 1이 됨
count--; // 다시 0이 됨
```

---

## 3. 비교 연산자
두 값을 비교하여 참(true) 또는 거짓(false)을 결과로 줍니다.

- `==` : 같다
- `!=` : 다르다
- `>` , `<` : 크다, 작다
- `>=` , `<=` : 크거나 같다, 작거나 같다

[예제 코드]
```
int age = 20;
Console.WriteLine(age == 20); // true
Console.WriteLine(age != 20); // false
Console.WriteLine(age > 19);  // true
```

---

## 4. 논리 연산자
여러 조건을 조합할 때 사용합니다.

- && (AND) : 두 조건이 모두 참일 때만 참
- || (OR) : 두 조건 중 하나만 참이어도 참
- ! (NOT) : 참을 거짓으로, 거짓을 참으로 뒤집음

[예제 코드]
```
int score = 85;
// 점수가 80점 이상 "이고" 90점 미만인지 확인
bool isBGrade = (score >= 80) && (score < 90); 

bool isHungry = true;
Console.WriteLine(!isHungry); // false
```

---

## 5. 대입 및 복합 대입 연산자
변수에 값을 넣거나, 계산 후 바로 자기 자신에게 대입할 때 사용합니다.

- = : 값을 대입
- +=, -=, *=, /= : 계산과 대입을 동시에

[예제 코드]
```
int money = 1000;
money += 500; // money = money + 500; 과 같음 (결과: 1500)
money -= 200; // 결과: 1300
```

---

## 6. Null 관련 연산자 (C# 특화)
데이터가 비어있는 상태(null)를 안전하게 다룰 때 사용합니다.

- ?? : 왼쪽 값이 null이면 오른쪽 값을 사용
- ?. : 왼쪽이 null이 아닐 때만 멤버에 접근 (Null 조건부 연산자)

[예제 코드]
```
string name = null;
string displayName = name ?? "홍길동"; // name이 없으므로 "홍길동" 선택

Console.WriteLine(displayName);
```

# [C# 자습 자료] 02. 제어문 (Control Flow)

프로그램의 흐름을 조건에 따라 나누거나(조건문), 특정 코드를 반복해서 실행(반복문)하는 방법을 학습합니다.

---

## 1. 조건문 (if, else if, else)
정해진 조건이 참(true)인지 거짓(false)인지에 따라 실행할 코드를 결정합니다.

[예제 코드]
```
int score = 85;
string name = "홍길동";

if (score >= 90)
{
    Console.WriteLine($"{name}님은 A학점입니다.");
}
else if (score >= 80)
{
    Console.WriteLine($"{name}님은 B학점입니다.");
}
else
{
    Console.WriteLine($"{name}님은 C학점 이하입니다.");
}
```

---

## 2. 선택문 (switch)
변수의 값에 따라 여러 경우(case) 중 하나를 선택하여 실행합니다.

`break`는 현재 실행 중인 **`switch` 블록을 즉시 빠져나가라**는 명령입니다. 
C#에서는 각 `case`의 끝에 `break`(또는 `return` 등)를 반드시 작성해야 하는 규칙이 있습니다.

[예제 코드]
```
int rank = 1;
string name = "홍길동";

switch (rank)
{
    case 1:
        Console.WriteLine($"{name}님은 금메달입니다.");
        break;
    case 2:
        Console.WriteLine($"{name}님은 은메달입니다.");
        break;
    case 3:
        Console.WriteLine($"{name}님은 동메달입니다.");
        break;
    default:
        Console.WriteLine($"{name}님은 참가상입니다.");
        break;
}
```

---

## 3. 반복문 - for
정해진 횟수만큼 코드를 반복할 때 주로 사용합니다.

[예제 코드]
```
// 홍길동이 1번부터 5번까지 출력됨
for (int i = 1; i <= 5; i++)
{
    Console.WriteLine($"번호: {i}, 이름: 홍길동");
}
```

---

## 4. 반복문 - while
조건이 참인 동안 계속해서 코드를 반복합니다. 횟수가 정해지지 않았을 때 유리합니다.

[예제 코드]
```
int count = 1;
while (count <= 3)
{
    Console.WriteLine($"홍길동이 {count}번째 외칩니다.");
    count++; // 이 부분이 없으면 무한 루프에 빠집니다.
}
```

---

## 5. 흐름 제어 (break, continue)
반복문 안에서 흐름을 강제로 조절할 때 사용합니다.

- break: 반복문을 즉시 종료하고 빠져나옵니다.
- continue: 현재 차례를 건너뛰고 다음 반복으로 넘어갑니다.

[예제 코드]
```
for (int i = 1; i <= 10; i++)
{
    if (i == 3) continue; // 3번은 건너뛰고 출력함
    if (i == 6) break;    // 6번이 되면 반복문을 아예 끝냄
    
    Console.WriteLine($"{i}번 홍길동");
}
```

# [C# 자습 자료] 03. 배열과 반복문 (Array & foreach)

동일한 종류의 데이터를 여러 개 묶어서 관리하는 방법과 이를 효율적으로 처리하는 방법을 학습합니다.

---

## 1. 배열 (Array)
배열은 같은 자료형의 상자들을 한 줄로 이어 붙인 것과 같습니다. 상자마다 번호(인덱스)가 매겨져 있습니다.

- **주의**: 번호(인덱스)는 항상 **0번**부터 시작합니다.
- 배열의 크기는 한 번 정하면 바꿀 수 없습니다.

[예제 코드]
```
// 1. 크기가 3인 정수 배열 선언
int[] scores = new int[3];
scores[0] = 90;
scores[1] = 85;
scores[2] = 70;

// 2. 선언과 동시에 초기화 (가장 많이 쓰는 방식)
string[] names = { "홍길동", "이순신", "강감찬" };

// 3. 특정 위치의 값 수정
names[0] = "홍길동 수정"; 
```

---

## 2. 배열의 길이 (Length)
배열 안에 상자가 몇 개 있는지 알고 싶을 때는 `Length` 속성을 사용합니다.

[예제 코드]
```
string[] names = { "홍길동", "이순신", "강감찬" };
Console.WriteLine($"등록된 인원수: {names.Length}"); // 출력 결과: 3
```

---

## 3. foreach 반복문 (배열 전용 반복문)
`for`문은 번호(i)를 직접 지정해야 하지만, `foreach`는 배열 안에 있는 데이터를 처음부터 끝까지 자동으로 하나씩 꺼내줍니다.

[예제 코드]
```
string[] names = { "홍길동", "이순신", "강감찬" };

// "names 배열에 있는 문자열(string)을 하나씩 꺼내서 n에 담아라"
foreach (string n in names)
{
    Console.WriteLine($"이름: {n}");
}
```

---

## 4. 배열과 foreach 활용하기
배열에 저장된 점수들의 합계와 평균을 구하는 예제입니다.

[예제 코드]
```
int[] scores = { 80, 90, 100 };
int sum = 0;

foreach (int s in scores)
{
    sum = sum + s; // 각 점수를 sum에 더함
}

double average = (double)sum / scores.Length;

Console.WriteLine($"홍길동님의 총점: {sum}");
Console.WriteLine($"홍길동님의 평균: {average}");
```

---

# [C# 자습 자료] 04. 메서드 (Method)

메서드는 반복되는 코드를 하나로 묶어놓은 '기능 보관함'입니다. 필요할 때마다 호출해서 사용할 수 있습니다.

## 1. 메서드의 구조
- **반환 타입**: 결과값의 종류 (없으면 void)
- **매개변수(Parameter)**: 입력받을 데이터

[예제 코드]
```
// 홍길동 이름을 출력하는 메서드 정의
static void PrintName()
{
    Console.WriteLine("이름: 홍길동");
}

// 메서드 호출
PrintName();
```

---

## 2. 입력값이 있는 메서드
메서드 이름 뒤 소괄호 ( ) 안에 데이터를 전달받을 변수를 적습니다.

[예제 코드]
```
static void Greet(string name)
{
    Console.WriteLine($"{name}님, 안녕하세요!");
}

// 호출할 때 데이터를 전달
Greet("홍길동");
```

---

## 3. 반환값(Return)이 있는 메서드
계산 결과를 밖으로 돌려줘야 할 때는 `void` 대신 결과값의 자료형을 적고 `return` 키워드를 사용합니다.

[예제 코드]
```
static int Add(int a, int b)
{
    return a + b; // 결과를 밖으로 보냄
}

// 메서드가 돌려준 값을 변수에 저장
int result = Add(10, 20);
Console.WriteLine($"홍길동님의 계산 결과: {result}");
```

# [C# 자습 자료] 05. 클래스와 객체 (Class & Object)

클래스는 실체가 없는 '설계도'이고, 객체는 그 설계도를 바탕으로 메모리에 실제로 만든 '제품'입니다.

---

## 1. 클래스의 구조
클래스는 크게 **데이터(필드)**와 **기능(메서드)**으로 구성됩니다.

- **필드(Field)**: 객체의 상태를 저장하는 변수
- **메서드(Method)**: 객체가 수행하는 동작

[예제 코드]
```
class Student
{
    // 필드 (데이터)
    public string name;
    public int age;

    // 메서드 (기능)
    public void Introduce()
    {
        Console.WriteLine($"이름은 {name}이고, 나이는 {age}살입니다.");
    }
}
```

---

## 2. 객체 생성과 사용 (new 키워드)
클래스라는 설계도를 사용해 실제 데이터인 '객체(인스턴스)'를 만듭니다.

[예제 코드]
```
// 1. 객체 생성 (설계도대로 제품 만들기)
Student student1 = new Student();

// 2. 필드 값 설정
student1.name = "홍길동";
student1.age = 20;

// 3. 메서드 호출
student1.Introduce();
student1?.Introduce(); // 만약 student1 이 null이라면 실행 안하고, null이 아닐때만 Introduce 메서드 실행
```

---

## 3. 생성자 (Constructor)
객체가 만들어질 때(new) 자동으로 호출되는 특별한 메서드입니다. 주로 데이터를 처음 세팅할 때 사용합니다.
* 클래스 이름과 똑같은 이름을 사용하며, 반환 타입(void 등)을 적지 않습니다.

[예제 코드]
```
class Student
{
    public string name;
    public int age;

    // 생성자: 객체를 만들 때 이름과 나이를 바로 정해줌
    public Student(string n, int a)
    {
        name = n;
        age = a;
    }
}
```

// 사용 예시
Student student2 = new Student("홍길동", 20); // 생성과 동시에 데이터 입력

---

## 4. 접근 제한자 (Access Modifiers)
클래스 내부의 정보를 어디까지 공개할지 결정합니다.

- **public**: 어디서든 접근 가능 (공개)
- **private**: 클래스 내부에서만 접근 가능 (비밀) - *작성하지 않으면 기본값은 private입니다.*

[예제 코드]
```
class BankAccount
{
    private int money; // 외부에서 함부로 수정 못하게 비밀로 설정

    public void Deposit(int amount)
    {
        money += amount;
        Console.WriteLine($"홍길동님의 계좌에 {amount}원이 입금되었습니다.");
    }
}
```

---

## 5. 프로퍼티 (Property)

프로퍼티는 클래스의 내부 데이터(필드)를 안전하게 보호하면서, 외부에서는 마치 변수처럼 편하게 값을 읽고 쓸 수 있게 해주는 통로입니다.
데이터를 필드에 저장할 때 조건을 검사하거나(set), 데이터를 읽어갈 때 가공해서 줄 수(get) 있습니다.

[예제 코드]
```
class Student
{
    private int age; // 외부에서 직접 만지지 못하게 private으로 숨김

    public int Age
    {
        get 
        { 
            return age; 
        }
        set 
        { 
            // 0보다 작은 값이 들어오지 못하게 방어함
            if (value < 0) 
            {
                Console.WriteLine("나이는 0보다 작을 수 없습니다.");
                age = 0;
            }
            else
            {
                age = value; 
            }
        }
    }
}

// 사용 예시
Student s = new Student();
s.Age = -5; // "나이는 0보다 작을 수 없습니다." 출력됨
Console.WriteLine($"홍길동의 나이: {s.Age}"); // 결과: 0
```

---

## 2. 자동 구현 프로퍼티 (Auto-Implemented Property)
특별한 조건 검사가 필요 없을 때 코드를 획기적으로 줄여주는 방식입니다. 실무에서 가장 많이 사용합니다.

[예제 코드]
```
class Person
{
    // 필드를 따로 만들 필요 없이 한 줄로 끝냄
    public string Name { get; set; }
    public string Address { get; set; }
}

// 사용 예시
Person p = new Person();
p.Name = "홍길동";
p.Address = "서울시";

Console.WriteLine($"{p.Name}은 {p.Address}에 삽니다.");
```

---

## 3. 읽기 전용 프로퍼티 (Read-Only)
값은 생성자에서만 정하고, 밖에서는 읽기만 가능하게 하여 데이터를 안전하게 관리합니다.

[예제 코드]
```
class Citizen
{
    // set을 빼거나 private set으로 만들면 외부에서 수정 불가
    public string IdNumber { get; private set; }

    public Citizen(string id)
    {
        IdNumber = id; // 생성자에서는 값 할당 가능
    }
}

// 사용 예시
Citizen c = new Citizen("123456-1234567");
Console.WriteLine($"홍길동의 주민번호: {c.IdNumber}");
// c.IdNumber = "000-000"; // 오류 발생: 밖에서는 값을 바꿀 수 없음
```

---

## 4. 프로퍼티를 사용하는 이유
1. **캡슐화**: 내부 데이터를 직접 노출하지 않아 객체의 독립성을 유지합니다.
2. **유효성 검사**: 잘못된 데이터(예: 음수 나이)가 입력되는 것을 막을 수 있습니다.
3. **디버깅**: 값을 변경하거나 읽을 때 중단점을 걸어 추적이 쉽습니다.

# [C# 자습 자료] 06. 상속과 다형성 (Inheritance & Polymorphism)

객체지향 프로그래밍의 핵심으로, 기존의 코드를 재사용하고 상황에 따라 다양한 기능을 수행하도록 만드는 방법입니다.

---

## 1. 상속 (Inheritance)
부모 클래스의 데이터와 기능을 자식 클래스가 그대로 물려받는 것입니다. 코드의 중복을 줄일 수 있습니다.

- **부모 클래스 (Parent/Base Class)**: 기능을 물려주는 클래스
- **자식 클래스 (Child/Derived Class)**: 기능을 물려받는 클래스 (클래스 이름 뒤에 `:` 를 붙임)

[예제 코드]
```
class Person
{
    public string Name { get; set; }
    public void Eat() => Console.WriteLine($"{Name}님이 식사를 합니다.");
}

// Person을 상속받는 Student 클래스
class Student : Person
{
    public void Study() => Console.WriteLine($"{Name}님이 공부를 합니다.");
}

// 사용 예시
Student student = new Student();
student.Name = "홍길동"; // 부모의 속성 사용
student.Eat();         // 부모의 메서드 사용
student.Study();       // 자신의 메서드 사용
```

---

## 2. 메서드 오버라이딩 (Overriding)
부모에게 물려받은 기능이 자식 클래스에게 맞지 않을 때, 자식이 내용을 **재정의**하는 것입니다.

- **virtual**: 부모 메서드에 "자식이 고쳐 써도 된다"고 허락함.
- **override**: 자식 메서드에서 "내가 다시 정의하겠다"고 선언함.

[예제 코드]
```
class Person
{
    public virtual void Work()
    {
        Console.WriteLine("홍길동님이 일을 합니다.");
    }
}

class Developer : Person
{
    public override void Work()
    {
        Console.WriteLine("홍길동님이 코딩을 합니다."); // 기능을 자식에 맞게 수정
    }
}
```

---

## 3. 다형성 (Polymorphism)
하나의 부모 타입으로 여러 종류의 자식 객체를 가리킬 수 있는 성질입니다. 겉모양은 부모인데 실제 동작은 자식 객체의 것으로 실행됩니다.

[예제 코드]
Person person1 = new Person();
Person person2 = new Developer(); // 부모 변수에 자식 객체를 담음

person1.Work(); // 출력: 홍길동님이 일을 합니다.
person2.Work(); // 출력: 홍길동님이 코딩을 합니다. (실제 객체인 Developer의 기능 실행)

---

## 4. 추상 클래스 (Abstract Class)
직접 객체를 만들 수는 없고, 자식 클래스들이 반드시 구현해야 할 **'표준 규격'**을 정해주는 클래스입니다.

[예제 코드]
```
abstract class Animal
{
    // 자식이 반드시 만들어야 하는 메서드 (내용은 없음)
    public abstract void MakeSound();
}

class Dog : Animal
{
    public override void MakeSound() => Console.WriteLine("홍길동님의 강아지: 멍멍!");
}
```

---

## 5. 인터페이스 (Interface)
클래스가 가져야 할 최소한의 규칙을 정의합니다. 상속과 비슷하지만 여러 개를 동시에 가질 수 있다는 장점이 있습니다.

[예제 코드]
```
interface IFlyable
{
    void Fly(); // 구현 내용 없이 이름만 정함
}

class Robot : IFlyable
{
    public void Fly() => Console.WriteLine("홍길동님의 로봇이 하늘을 납니다.");
}
```

# [C# 자습 자료] 07. 예외 처리와 파일 입출력

프로그램 실행 중 발생하는 오류를 안전하게 관리하고, 데이터를 파일에 저장하거나 불러오는 방법을 학습합니다.

---

## 1. 예외 처리 (Exception Handling)
프로그램이 예상치 못한 상황(예: 숫자를 0으로 나누기, 없는 파일 열기)을 만났을 때 멈추지 않고 대처하도록 만드는 장치입니다.

- **try**: 오류가 발생할 가능성이 있는 코드를 감쌉니다.
- **catch**: 오류가 발생했을 때 실행할 해결 코드를 적습니다.
- **finally**: 오류 발생 여부와 상관없이 마지막에 무조건 실행합니다.

[예제 코드]
```
try
{
    int[] numbers = { 1, 2, 3 };
    Console.WriteLine(numbers[5]); // 존재하지 않는 번호 접근 (오류 발생)
}
catch (Exception ex)
{
    Console.WriteLine($"홍길동님, 오류가 발생했습니다: {ex.Message}");
}
finally
{
    Console.WriteLine("프로그램을 안전하게 종료합니다.");
}
```

---

## 2. 파일 쓰기 (File Write)
데이터를 텍스트 파일(.txt)로 저장하는 방법입니다. `System.IO` 네임스페이스를 사용합니다.

[예제 코드]
```
using System.IO;

string path = "test.txt";
string content = "이 내용은 홍길동이 작성한 파일 데이터입니다.";

// 파일이 없으면 새로 만들고, 있으면 덮어씁니다.
File.WriteAllText(path, content);
Console.WriteLine("파일 저장이 완료되었습니다.");
```

---

## 3. 파일 읽기 (File Read)
저장된 파일의 내용을 불러와서 화면에 출력합니다.

[예제 코드]
```
if (File.Exists("test.txt")) // 파일이 있는지 확인
{
    string readText = File.ReadAllText("test.txt");
    Console.WriteLine($"파일 내용: {readText}");
}
else
{
    Console.WriteLine("홍길동님, 읽을 파일이 존재하지 않습니다.");
}
```

---

## 4. StreamWriter와 StreamReader
파일 전체를 한 번에 읽지 않고, 한 줄씩 쓰거나 읽을 때 효율적입니다. `using` 문을 사용하면 사용 후 파일을 자동으로 닫아줍니다.

[예제 코드]
```
// 한 줄씩 쓰기
using (StreamWriter sw = new StreamWriter("log.txt"))
{
    sw.WriteLine("첫 번째 줄: 홍길동");
    sw.WriteLine("두 번째 줄: C# 학습 중");
}

// 한 줄씩 읽기
using (StreamReader sr = new StreamReader("log.txt"))
{
    while (!sr.EndOfStream) // 파일 끝까지 반복
    {
        Console.WriteLine(sr.ReadLine());
    }
}
```

---

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

# [C# 자습 자료] 09. 람다식과 LINQ (데이터 처리의 혁신)

복잡한 반복문을 한 줄로 줄여주고, 데이터를 SQL처럼 쉽고 직관적으로 추출하는 방법을 학습합니다.
```

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
