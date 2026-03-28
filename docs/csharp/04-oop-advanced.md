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
    public void Eat() {
        Console.WriteLine($"{Name}님이 식사를 합니다.");
    }
}

// Person을 상속받는 Student 클래스
class Student : Person
{
    public void Study() {
        Console.WriteLine($"{Name}님이 공부를 합니다.");
    }
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
    public override void MakeSound() {
        Console.WriteLine("홍길동님의 강아지: 멍멍!");
    }
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
    public void Fly()
    {
        Console.WriteLine("홍길동님의 로봇이 하늘을 납니다.");
    }
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
