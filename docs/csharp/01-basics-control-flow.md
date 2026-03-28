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
