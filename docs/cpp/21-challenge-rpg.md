# Chapter 21 도전! 콘솔 RPG 구조화

## 학습 목표

- 플레이어 생성 → 전투 → 결과까지 최소 RPG 루프를 구현할 수 있다.
- 클래스·STL 컨테이너·예외를 한 프로그램에 역할별로 나눌 수 있다.
- 잘못된 입력과 파일 실패를 예외로 처리하는 뼈대를 둘 수 있다.

## 본문

### 21-1 범위와 RPG 루프

**RPG**는 Role-Playing Game의 약자입니다. 플레이어가 캐릭터를 키우며 진행하는 장르를 말합니다. 이 장은 새 문법을 늘리기보다, 11~20장에서 배운 **클래스·상속·컨테이너·예외·파일 입출력**을 한 콘솔 프로젝트에 모읍니다.

기능을 한꺼번에 넣지 않습니다. 먼저 **메뉴 → 전투 → 종료**만 완성하고, 그다음에 인벤토리·저장을 붙입니다. 한 클래스에 입력·전투·출력·저장을 모두 넣지 않는 것이 목표입니다.

| 단계 | 할 일 |
|---|---|
| 메뉴 | 시작·전투·종료 선택 |
| 전투 | 턴 기반 공격·피격·승패 |
| 확장 | 아이템 목록, 파일 저장 |

| 용어 | 의미 |
|---|---|
| 게임 루프 | 입력 → 상태 갱신 → 출력을 반복하는 구조 |
| 모듈 분리 | 역할을 클래스·함수 단위로 나눠 수정 범위를 줄이는 설계 |

### 21-2 역할 분리와 최소 골격

역할을 먼저 표로 고정합니다.

| 클래스 | 책임 |
|---|---|
| `Player` | 이름·체력·공격력, 피해 받기 |
| `Enemy` | 적 이름·체력·공격력 |
| `Battle` | 한 판 전투 루프와 로그 |
| `Inventory` | 아이템 이름 목록. `vector<string>` |
| `Game` | 메뉴 루프와 예외 처리 |

16장 전투와 달리, 여기서는 **STL**로 인벤토리를 두고 **예외**로 잘못된 메뉴 입력을 막습니다. `stoi`가 실패하거나 메뉴 번호가 범위를 벗어나면 `runtime_error`를 던져 `Game::Run`의 `catch`에서 메시지를 출력합니다.

아래를 타이핑해 실행해 보세요. 메뉴에서 `1`을 고르면 전투 로그와 승패가 나오고, 잘못된 입력이면 예외 메시지가 난 뒤 메뉴로 돌아옵니다.

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>
using namespace std;

class Player
{
public:
    Player(string name, int hp, int atk)
        : name(name), hp(hp), atk(atk)
    {
    }

    string GetName() const { return name; }
    int GetAtk() const { return atk; }
    int GetHp() const { return hp; }
    bool IsAlive() const { return hp > 0; }

    void Hit(int damage)
    {
        hp -= damage;
        if (hp < 0)
        {
            hp = 0;
        }
    }

private:
    string name;
    int hp;
    int atk;
};

class Enemy
{
public:
    Enemy(string name, int hp, int atk)
        : name(name), hp(hp), atk(atk)
    {
    }

    string GetName() const { return name; }
    int GetAtk() const { return atk; }
    bool IsAlive() const { return hp > 0; }

    void Hit(int damage)
    {
        hp -= damage;
        if (hp < 0)
        {
            hp = 0;
        }
    }

private:
    string name;
    int hp;
    int atk;
};

class Inventory
{
public:
    void Add(const string& item)
    {
        items.push_back(item);
    }

    void Print() const
    {
        cout << "인벤토리:";
        for (const string& item : items)
        {
            cout << " " << item;
        }
        cout << endl;
    }

private:
    vector<string> items;
};

class Battle
{
public:
    static void Fight(Player& player, Enemy& enemy)
    {
        while (player.IsAlive() && enemy.IsAlive())
        {
            enemy.Hit(player.GetAtk());
            cout << player.GetName() << " -> " << enemy.GetName()
                 << " 피해 " << player.GetAtk() << endl;
            if (!enemy.IsAlive())
            {
                break;
            }

            player.Hit(enemy.GetAtk());
            cout << enemy.GetName() << " -> " << player.GetName()
                 << " 피해 " << enemy.GetAtk() << endl;
        }

        if (player.IsAlive())
        {
            cout << "승리" << endl;
        }
        else
        {
            cout << "패배" << endl;
        }
    }
};

class Game
{
public:
    void Run()
    {
        Player hero("용사", 40, 12);
        Inventory bag;
        bag.Add("포션");
        bag.Add("검");

        while (true)
        {
            try
            {
                cout << "1.전투 2.인벤토리 0.종료 > ";
                string line;
                if (!(cin >> line))
                {
                    throw runtime_error("입력 실패");
                }

                int choice = stoi(line);
                if (choice == 0)
                {
                    cout << "종료" << endl;
                    break;
                }
                if (choice == 1)
                {
                    Enemy slime("슬라임", 30, 5);
                    Battle::Fight(hero, slime);
                }
                else if (choice == 2)
                {
                    bag.Print();
                }
                else
                {
                    throw runtime_error("메뉴 번호 오류");
                }
            }
            catch (const exception& e)
            {
                cout << e.what() << endl;
            }
        }
    }
};

int main()
{
    Game game;
    game.Run();
    return 0;
}
```

**예상 출력** (`1` → 전투, `2` → 인벤토리, `9` → 오류, `0` → 종료 순으로 입력한 예)

```text
1.전투 2.인벤토리 0.종료 > 1
용사 -> 슬라임 피해 12
슬라임 -> 용사 피해 5
용사 -> 슬라임 피해 12
슬라임 -> 용사 피해 5
용사 -> 슬라임 피해 12
승리
1.전투 2.인벤토리 0.종료 > 2
인벤토리: 포션 검
1.전투 2.인벤토리 0.종료 > 9
메뉴 번호 오류
1.전투 2.인벤토리 0.종료 > 0
종료
```

**코드 해석**

- `Player`·`Enemy`는 상태와 피해만 담당합니다. 전투 규칙은 `Battle`에 둡니다.
- `Inventory`는 `vector<string>`으로 아이템 이름을 보관합니다.
- `Game::Run`의 `try`/`catch`가 잘못된 메뉴와 `stoi` 실패를 받아 루프를 유지합니다.
- 새 몬스터를 넣어도 `Battle::Fight` 시그니처는 `Player`와 `Enemy`만 알면 됩니다.

### 21-3 저장과 품질 체크

최소 루프가 돌아가면 **파일 저장**을 붙입니다. 12장 `fstream`으로 이름·체력을 한 줄에 쓰고, 열기 실패 시 예외를 던집니다. RAII 관점에서는 `ofstream`/`ifstream` 객체가 스코프를 나갈 때 파일이 닫히므로, 예외가 나도 스트림 정리는 객체 소멸에 맡깁니다.

아래는 저장·불러오기만 따로 확인하는 짧은 예입니다. 실행 후 같은 폴더에 `hero.txt`가 생기고, 다시 읽은 값이 출력되는지 확인해 보세요.

```cpp
#include <fstream>
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

void SavePlayer(const string& path, const string& name, int hp)
{
    ofstream out(path);
    if (!out)
    {
        throw runtime_error("저장 실패");
    }
    out << name << " " << hp << endl;
}

void LoadPlayer(const string& path, string& name, int& hp)
{
    ifstream in(path);
    if (!in)
    {
        throw runtime_error("불러오기 실패");
    }
    if (!(in >> name >> hp))
    {
        throw runtime_error("형식 오류");
    }
}

int main()
{
    try
    {
        SavePlayer("hero.txt", "용사", 35);
        string name;
        int hp = 0;
        LoadPlayer("hero.txt", name, hp);
        cout << name << " HP:" << hp << endl;
    }
    catch (const exception& e)
    {
        cout << e.what() << endl;
    }
    return 0;
}
```

**예상 출력**

```text
용사 HP:35
```

**코드 해석**

- `ofstream`이 열리지 않으면 바로 예외를 던져 호출부가 처리합니다.
- 불러오기도 파일·형식 실패를 같은 방식으로 알립니다.
- `Game`에 메뉴 `3.저장`을 추가할 때는 이 함수들을 호출하고, 실패 메시지는 기존 `catch`에 맡기면 됩니다.

| 항목 | 최소 완료 기준 |
|---|---|
| 전투 | 턴 기반 공격·피격·승패 로그 |
| 데이터 | `vector` 등으로 목록 보관 |
| 안정성 | 잘못된 입력·파일 실패를 예외로 처리 |
| 확장 | 새 적·아이템 추가 시 수정 범위가 예측 가능 |

이 장에서는 **구조화된 최소 RPG**까지입니다. 복사/이동·헤더 분리·CMake는 이후 장에서 이어갑니다.

### 연습문제

**문제 1**

- 문제: 시작·전투·종료 3단계 메뉴 루프를 구현하세요.
- 입력: 메뉴 번호. 예 `1`, `0`
- 출력: 전투 시 턴 로그와 `승리`/`패배`, 종료 시 `종료`. 잘못된 번호면 오류 메시지
- 조건: `try`/`catch`로 잘못된 입력 처리. `Player`와 전투 규칙을 분리

**문제 2**

- 문제: 아이템 이름을 담는 인벤토리를 `vector`로 만들고 목록을 출력하세요.
- 입력: 없음. 코드에서 아이템 2개 이상 추가
- 출력: 예 `인벤토리: 포션 검`
- 조건: 출력 전용 멤버 함수 또는 자유 함수로 목록 출력

**문제 3**

- 문제: 플레이어 이름과 체력을 파일에 저장하고 다시 불러오세요.
- 입력: 없음. 저장할 이름·체력은 코드 상수로 두어도 됨
- 출력: 불러온 뒤 `이름 HP:숫자` 형태
- 조건: 파일 열기 실패 시 예외. `fstream` 사용

### 정답 포인트

- 문제 1: 본문 `Game::Run`처럼 메뉴 분기 + `Battle::Fight`
- 문제 2: `push_back` 후 범위 `for`로 출력
- 문제 3: `SavePlayer`/`LoadPlayer`처럼 실패 시 `throw`, 성공 시 값 출력
- 공통: 최소 루프 완성 후 인벤토리·저장 확장. 책임은 클래스 단위로 유지

---

[상위 문서로 돌아가기](./README.md)
