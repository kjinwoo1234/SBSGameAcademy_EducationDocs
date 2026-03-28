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

# [C# 자습 자료] static 메서드와 필드 (공유 자원)

`static` 키워드는 **"객체를 만들지 않고도 클래스 이름으로 바로 접근할 수 있는 공유 상자"**를 의미합니다.

---

## 1. static의 개념
일반적인 변수나 메서드는 `new`를 통해 객체를 만들어야 사용할 수 있지만, `static`이 붙은 데이터는 프로그램이 시작될 때 메모리에 딱 하나만 만들어져서 모든 객체가 **공유**합니다.

- **Non-static (인스턴스)**: 각 객체마다 별도로 존재함 (나만의 소지품)
- **static (정적)**: 클래스 전체에서 하나만 존재함 (공용 도구함)

---

## 2. static 필드 (공유 데이터)
모든 객체가 공동으로 관리해야 하는 데이터를 저장할 때 사용합니다. 예를 들어, 지금까지 생성된 학생의 총 숫자를 관리할 때 유용합니다.

[예제 코드]
```
class Student
{
    public string name;      // 각 학생마다 이름은 다름 (인스턴스 필드)
    public static int count; // 모든 학생이 공유하는 총 인원수 (static 필드)

    public Student(string n)
    {
        name = n;
        count++; // 학생이 새로 태어날 때마다 공유 상자 값을 1씩 증가
    }
}

// 사용 예시
Student s1 = new Student("홍길동");
Student s2 = new Student("임꺽정");

Console.WriteLine($"학생 이름: {s1.name}"); // 홍길동
Console.WriteLine($"총 학생 수: {Student.count}"); // 객체 이름(s1)이 아닌 클래스 이름(Student)으로 접근
// 결과: 2
```

---

## 3. static 메서드 (공용 기능)
객체의 상태(인스턴스 필드)를 사용하지 않고, 전달받은 값으로만 계산하거나 공통 기능을 제공할 때 사용합니다.

- `static` 메서드 안에서는 `static`이 아닌 변수(인스턴스 변수)를 사용할 수 없습니다. (상자는 아직 없는데 공용 도구만 나와 있는 상태이기 때문)

[예제 코드]
```
class Calculator
{
    // 입력받은 값만 계산하면 되므로 static이 적합함
    public static int Add(int a, int b)
    {
        return a + b;
    }
}

// 사용 예시 (new Calculator()를 할 필요가 없음)
int result = Calculator.Add(10, 20);
Console.WriteLine($"홍길동의 계산 결과: {result}");
```

---

## 4. static을 사용하는 이유
1. **메모리 효율**: 객체마다 기능을 따로 만들지 않고 하나를 공유하므로 메모리를 아낍니다.
2. **편의성**: 자주 쓰는 도구(예: `Math.Abs()`, `Console.WriteLine()`)를 굳이 `new` 하지 않고 바로 쓸 수 있습니다.
3. **데이터 공유**: 프로그램 전체에서 공통으로 유지해야 하는 값(설정 정보, 카운트 등)을 관리하기 좋습니다.

---

## 5. 대표적인 static 예시: Main 메서드
C# 프로그램의 시작점인 `Main` 메서드가 항상 `static`인 이유는, 프로그램이 실행될 때 아직 아무 객체도 만들어지지 않은 상태에서 바로 실행되어야 하기 때문입니다.

[예제 코드]
```
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("홍길동의 프로그램 시작!");
    }
}
```
