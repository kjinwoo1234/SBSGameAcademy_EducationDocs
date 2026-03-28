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
