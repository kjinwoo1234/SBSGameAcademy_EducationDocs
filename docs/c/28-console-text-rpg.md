# Chapter 28 콘솔 텍스트 RPG 만들기

## 학습 목표

- 구조체로 캐릭터 상태를 묶고, 포인터를 넘기는 함수로 상태를 바꾼다.
- 메뉴 입력 → 상태 갱신 → 결과 출력을 반복하는 턴제 전투 루프를 만든다.
- 파일 입출력으로 캐릭터 상태를 저장하고 불러온다.
- 기능별로 `.h` / `.c`를 나눠 하나의 작은 프로젝트로 묶는다.

## 본문

### 28-1 텍스트 RPG의 구조

**RPG**는 Role-Playing Game의 약자로, 플레이어가 캐릭터를 키우며 진행하는 장르입니다. **텍스트 RPG**는 그림 없이 글자만으로 상태를 보여 주고, 숫자 메뉴로 행동을 고릅니다. 화면 좌표나 실시간 입력이 필요 없어서 지금까지 배운 C 문법만으로 끝까지 만들 수 있습니다.

이 장은 새 문법을 배우지 않습니다. 구조체(21~22장), 포인터(12~14장), 파일 입출력(24장), 파일 분할(27장)을 **한 프로그램에 모으는 장**입니다.

텍스트 RPG는 한 턴마다 같은 순서를 반복합니다.

| 단계 | 할 일 |
|---|---|
| 출력 | 플레이어와 적의 체력, 메뉴 표시 |
| 입력 | `scanf`로 메뉴 번호 읽기 |
| 갱신 | 공격·회복 계산, 적의 반격 |
| 판정 | 한쪽 체력이 `0` 이하면 전투 종료 |

| 용어 | 의미 |
|---|---|
| 턴 | 플레이어와 적이 한 번씩 행동하는 단위 |
| 게임 루프 | 출력 → 입력 → 갱신 → 판정을 반복하는 구조 |

### 28-2 캐릭터 구조체

플레이어와 적은 모두 **이름·체력·최대 체력·공격력·방어력**을 가집니다. 같은 모양이므로 구조체 하나로 둘 다 표현합니다.

아래를 실행해 두 캐릭터의 상태가 한 줄씩 출력되는지 확인해 보세요.

```c
#include <stdio.h>

typedef struct
{
    char name[20];
    int hp;
    int max_hp;
    int atk;
    int def;
} Character;

void print_status(const Character *c)
{
    printf("%s HP %d/%d ATK %d DEF %d\n",
           c->name, c->hp, c->max_hp, c->atk, c->def);
}

int main(void)
{
    Character hero = {"Hero", 30, 30, 8, 2};
    Character slime = {"Slime", 20, 20, 5, 1};

    print_status(&hero);
    print_status(&slime);
    return 0;
}
```

**예상 출력**

```text
Hero HP 30/30 ATK 8 DEF 2
Slime HP 20/20 ATK 5 DEF 1
```

**코드 해석**

- `typedef struct { ... } Character;`로 캐릭터 하나에 필요한 값을 묶었습니다.
- 초기화 목록 `{"Hero", 30, 30, 8, 2}`는 멤버 선언 순서대로 값을 넣습니다.
- `print_status`는 구조체 전체를 복사하지 않도록 **주소**를 받습니다. 값을 바꾸지 않으므로 `const`를 붙였습니다.
- 포인터로 받은 구조체의 멤버는 `->`로 읽습니다.

### 28-3 공격 함수

공격은 **공격하는 쪽의 공격력에서 맞는 쪽의 방어력을 뺀 값**만큼 체력을 깎는다고 정합니다. 방어력이 더 커도 최소 `1`은 들어가게 하고, 체력은 `0` 아래로 내려가지 않게 막습니다.

맞는 쪽의 체력이 **바뀌어야** 하므로 이번에는 `const` 없이 주소를 받습니다.

아래를 실행해 피해량과 남은 체력이 출력되는지 확인해 보세요.

```c
#include <stdio.h>

typedef struct
{
    char name[20];
    int hp;
    int max_hp;
    int atk;
    int def;
} Character;

int attack(const Character *attacker, Character *target)
{
    int damage = attacker->atk - target->def;

    if (damage < 1)
    {
        damage = 1;
    }

    target->hp = target->hp - damage;
    if (target->hp < 0)
    {
        target->hp = 0;
    }
    return damage;
}

int main(void)
{
    Character hero = {"Hero", 30, 30, 8, 2};
    Character slime = {"Slime", 20, 20, 5, 1};
    int dmg;

    dmg = attack(&hero, &slime);
    printf("%s -> %s : %d damage\n", hero.name, slime.name, dmg);
    printf("%s HP %d\n", slime.name, slime.hp);
    return 0;
}
```

**예상 출력**

```text
Hero -> Slime : 7 damage
Slime HP 13
```

**코드 해석**

- `damage`는 `8 - 1`로 `7`입니다.
- `target->hp`를 직접 바꾸므로 `main`의 `slime.hp`가 `13`이 됩니다.
- 공격한 쪽은 바뀌지 않으므로 `attacker`에는 `const`를 붙였습니다.
- 피해량을 돌려주면 호출한 쪽에서 로그를 찍기 쉽습니다.

### 28-4 전투 루프

이제 메뉴로 행동을 고르는 턴제 전투를 만듭니다. 메뉴는 `1` 공격, `2` 회복, `3` 도망 세 가지입니다. 회복은 `10`만큼 올리되 최대 체력을 넘지 않게 합니다. 플레이어가 행동한 뒤 적이 살아 있으면 적이 반격합니다.

메뉴 번호에 따라 하는 일이 갈리므로 `switch`를 씁니다.

아래를 실행하고 `1`을 세 번 입력해 보세요.

```c
#include <stdio.h>

typedef struct
{
    char name[20];
    int hp;
    int max_hp;
    int atk;
    int def;
} Character;

int attack(const Character *attacker, Character *target)
{
    int damage = attacker->atk - target->def;

    if (damage < 1)
    {
        damage = 1;
    }
    target->hp = target->hp - damage;
    if (target->hp < 0)
    {
        target->hp = 0;
    }
    return damage;
}

void heal(Character *c, int amount)
{
    c->hp = c->hp + amount;
    if (c->hp > c->max_hp)
    {
        c->hp = c->max_hp;
    }
}

int main(void)
{
    Character hero = {"Hero", 30, 30, 8, 2};
    Character slime = {"Slime", 20, 20, 5, 1};
    int menu;
    int running = 1;

    while (running && hero.hp > 0 && slime.hp > 0)
    {
        printf("[%s %d/%d] vs [%s %d/%d]\n",
               hero.name, hero.hp, hero.max_hp,
               slime.name, slime.hp, slime.max_hp);
        printf("1.attack 2.heal 3.run > ");
        scanf("%d", &menu);

        switch (menu)
        {
        case 1:
            printf("hit %d\n", attack(&hero, &slime));
            break;
        case 2:
            heal(&hero, 10);
            printf("heal\n");
            break;
        case 3:
            running = 0;
            break;
        default:
            printf("wrong menu\n");
            break;
        }

        if (running && slime.hp > 0)
        {
            printf("enemy hit %d\n", attack(&slime, &hero));
        }
    }

    if (slime.hp == 0)
    {
        printf("win\n");
    }
    else if (hero.hp == 0)
    {
        printf("lose\n");
    }
    else
    {
        printf("run away\n");
    }
    return 0;
}
```

**예상 출력** (입력 `1`, `1`, `1`)

```text
[Hero 30/30] vs [Slime 20/20]
1.attack 2.heal 3.run > 1
hit 7
enemy hit 3
[Hero 27/30] vs [Slime 13/20]
1.attack 2.heal 3.run > 1
hit 7
enemy hit 3
[Hero 24/30] vs [Slime 6/20]
1.attack 2.heal 3.run > 1
hit 7
win
```

**코드 해석**

- `while` 조건은 "도망치지 않았고, 둘 다 살아 있는 동안"입니다.
- `switch`로 메뉴마다 다른 함수를 부릅니다. 범위 밖 번호는 `default`에서 안내만 합니다.
- 적은 살아 있을 때만 반격합니다. 마지막 턴에 슬라임 체력이 `0`이 되어 반격이 없습니다.
- 루프가 끝난 뒤 체력을 보고 승리·패배·도망 중 하나를 출력합니다.

### 28-5 저장과 불러오기

전투가 끝난 플레이어 상태를 파일로 남기면 다음 실행에서 이어서 할 수 있습니다. 사람이 열어 볼 수 있게 **텍스트 파일**에 한 줄로 저장합니다.

| 함수 | 역할 |
|---|---|
| `fprintf` | 구조체 멤버를 형식대로 파일에 쓰기 |
| `fscanf` | 같은 형식으로 다시 읽어 멤버에 넣기 |

쓰는 형식과 읽는 형식을 **똑같이** 맞추는 것이 핵심입니다.

아래를 실행해 저장한 값이 그대로 다시 읽히는지 확인해 보세요.

```c
#include <stdio.h>

typedef struct
{
    char name[20];
    int hp;
    int max_hp;
    int atk;
    int def;
} Character;

int save_character(const char *path, const Character *c)
{
    FILE *fp = fopen(path, "w");

    if (fp == NULL)
    {
        return 0;
    }
    fprintf(fp, "%s %d %d %d %d\n",
            c->name, c->hp, c->max_hp, c->atk, c->def);
    fclose(fp);
    return 1;
}

int load_character(const char *path, Character *c)
{
    FILE *fp = fopen(path, "r");
    int count;

    if (fp == NULL)
    {
        return 0;
    }
    count = fscanf(fp, "%19s %d %d %d %d",
                   c->name, &c->hp, &c->max_hp, &c->atk, &c->def);
    fclose(fp);
    return count == 5;
}

int main(void)
{
    Character hero = {"Hero", 24, 30, 8, 2};
    Character loaded;

    if (!save_character("save.txt", &hero))
    {
        printf("save fail\n");
        return 1;
    }
    if (!load_character("save.txt", &loaded))
    {
        printf("load fail\n");
        return 1;
    }
    printf("%s HP %d/%d\n", loaded.name, loaded.hp, loaded.max_hp);
    return 0;
}
```

**예상 출력**

```text
Hero HP 24/30
```

**코드 해석**

- `save_character`는 멤버 다섯 개를 공백으로 구분해 한 줄로 씁니다.
- `load_character`는 같은 순서로 읽습니다. `%19s`는 `name` 배열 크기 `20`을 넘지 않게 막습니다.
- `fscanf`가 돌려준 값은 읽어 낸 항목 수입니다. `5`가 아니면 파일이 망가진 것으로 보고 실패를 돌려줍니다.
- 두 함수 모두 `fopen` 실패를 먼저 확인합니다.

### 28-6 파일 나누기

지금까지 만든 함수를 27장 방식으로 역할별 파일에 나눕니다.

| 파일 | 담는 것 |
|---|---|
| `character.h` / `character.c` | `Character` 정의, `print_status`, `attack`, `heal` |
| `save.h` / `save.c` | `save_character`, `load_character` |
| `main.c` | 불러오기 → 전투 루프 → 저장 순서로 조합 |

`Character` 정의는 여러 파일이 함께 쓰므로 `character.h`에 둡니다. `save.h`는 `Character`를 알아야 하므로 `character.h`를 포함합니다.

```c
/* character.h */
#ifndef CHARACTER_H
#define CHARACTER_H

typedef struct
{
    char name[20];
    int hp;
    int max_hp;
    int atk;
    int def;
} Character;

void print_status(const Character *c);
int attack(const Character *attacker, Character *target);
void heal(Character *c, int amount);

#endif
```

```c
/* save.h */
#ifndef SAVE_H
#define SAVE_H

#include "character.h"

int save_character(const char *path, const Character *c);
int load_character(const char *path, Character *c);

#endif
```

`main.c`는 이 두 헤더만 포함하고, 함수 본체는 각각 `character.c`, `save.c`에 둡니다. 전투 규칙을 바꿀 때는 `character.c`만, 저장 형식을 바꿀 때는 `save.c`만 고치면 됩니다.

### 연습문제

**문제 1**
- 문제: `Character`에 `int gold;` 멤버를 추가하고, 적을 이기면 `gold`를 `10` 올린 뒤 `gold=10`을 출력하세요.
- 입력: 전투 메뉴에서 `1` 반복
- 출력: 마지막 두 줄
  ```text
  win
  gold=10
  ```
- 조건: 초기화 목록과 `print_status`도 새 멤버에 맞게 고칠 것

**문제 2**
- 문제: 적을 `Character enemies[3]` 배열로 두고, 차례로 세 번 전투하세요. 플레이어 체력은 전투 사이에 이어집니다.
- 입력: 전투 메뉴 번호
- 출력: 세 마리를 모두 이기면 마지막에
  ```text
  clear
  ```
  중간에 지면 `lose`
- 조건: 한 번의 전투를 `int battle(Character *hero, Character *enemy)` 함수로 분리하고, 이기면 `1`을 돌려줄 것

**문제 3**
- 문제: 28-6 표대로 `character.h/.c`, `save.h/.c`, `main.c`로 나누고, 실행 시 `save.txt`가 있으면 불러와서 전투를 시작하고 끝나면 다시 저장하세요.
- 입력: 전투 메뉴 번호
- 출력: 두 번째 실행 첫 줄에 이전 실행에서 끝난 체력이 보일 것
- 조건: 모든 헤더에 include guard. 불러오기에 실패하면 기본값 캐릭터로 시작

### 정답 포인트

- 문제 1: 구조체에 멤버를 추가하면 초기화 목록 순서와 출력·저장 형식을 함께 맞춘다
- 문제 2: 구조체 배열을 `for`로 돌며 `battle(&hero, &enemies[i])`를 호출. 반환값이 `0`이면 바로 종료
- 문제 3: `main`은 조합만, 규칙은 `character.c`, 저장은 `save.c`. 로드 실패 시 기본값으로 초기화
- 공통: 상태는 구조체로 묶고, 바꾸는 함수는 주소를 받으며, 바꾸지 않는 함수는 `const` 주소를 받음

---

[상위 문서로 돌아가기](./README.md)
