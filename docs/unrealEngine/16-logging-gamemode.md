# Chapter 16 로깅과 게임모드

## 학습 목표

- `UE_LOG`로 출력 로그에 값을 찍고 상세도를 구분해 쓸 수 있다.
- 프로젝트 전용 로그 카테고리와 호출 위치를 찍는 매크로를 만들 수 있다.
- 게임모드가 지정하는 클래스들의 역할을 구분할 수 있다.
- 게임모드를 만들어 레벨에 적용하고 디폴트 폰을 바꿀 수 있다.

## 본문

### 16-1 출력 로그와 UE_LOG

블루프린트에서는 `Print String`으로 값을 확인했습니다. C++에서도 코드가 실행되는지, 변수 값이 얼마인지 확인할 방법이 필요합니다. 프로그램이 실행 중에 남기는 기록을 **로그**라고 합니다.

언리얼의 로그는 **출력 로그** 창에서 봅니다. 에디터 아래쪽의 **출력 로그** 탭을 누르거나 메뉴 바의 **창 → 출력 로그**로 엽니다.

로그를 남기는 매크로는 `UE_LOG`입니다. `MyActor`의 `BeginPlay`에 아래 코드를 넣고 빌드한 뒤 플레이해 보세요.

```cpp
void AMyActor::BeginPlay()
{
	Super::BeginPlay();

	UE_LOG(LogTemp, Warning, TEXT("MyActor BeginPlay"));
	UE_LOG(LogTemp, Warning, TEXT("Power = %f, Level = %d"), Power, Level);
	UE_LOG(LogTemp, Warning, TEXT("Name = %s"), *GetName());
}
```

```text
LogTemp: Warning: MyActor BeginPlay
LogTemp: Warning: Power = 10.000000, Level = 1
LogTemp: Warning: Name = MyActor_0
```

**코드 해석**

- `UE_LOG(카테고리, 상세도, TEXT("내용"), 값...)` 형태로 씁니다.
- `LogTemp`는 임시 확인용으로 누구나 쓸 수 있는 카테고리입니다.
- `Warning`은 상세도입니다. 출력 로그에 노란색으로 표시되어 눈에 잘 띕니다.
- `%d`는 정수, `%f`는 실수, `%s`는 문자열이 들어갈 자리입니다. C 언어의 `printf`와 같은 방식입니다.
- `%s`에 `FString`을 넣을 때는 앞에 `*`를 붙입니다. `GetName()`은 액터의 이름을 `FString`으로 돌려줍니다.

### 16-2 카테고리와 상세도

출력 로그의 각 줄은 `LogTemp: Warning: 내용` 형태입니다. 맨 앞 단어가 **카테고리**이고, 로그를 주제별로 나누는 이름표입니다. 엔진이 남기는 로그는 `LogEditor`, `LogBlueprint`처럼 기능마다 카테고리가 다릅니다. 출력 로그 창의 **필터** 버튼으로 특정 카테고리만 골라 볼 수 있습니다.

두 번째 단어는 **상세도**이고, 로그가 얼마나 중요한지를 나타냅니다.

| 상세도 | 의미 | 표시 |
|---|---|---|
| `Fatal` | 더 진행할 수 없는 오류. 프로그램이 종료된다 | 빨간색 |
| `Error` | 오류 | 빨간색 |
| `Warning` | 경고 | 노란색 |
| `Display` | 일반 정보 | 흰색 |
| `Log` | 일반 정보. 파일에 기록된다 | 흰색 |
| `Verbose` | 자세한 정보. 평소에는 보이지 않는다 | |
| `VeryVerbose` | 매우 자세한 정보. 평소에는 보이지 않는다 | |

표의 위쪽일수록 중요합니다. 개발 중 임시로 확인하는 로그는 눈에 띄는 `Warning`을 자주 쓰고, 실제 문제 상황에는 `Error`를 씁니다.

### 16-3 프로젝트 전용 카테고리 만들기

`LogTemp`는 모든 사람이 쓰기 때문에 로그가 많아지면 내가 남긴 것을 찾기 어렵습니다. 프로젝트 전용 카테고리를 만들어 둡니다.

`Source/Practice` 폴더에는 프로젝트 이름과 같은 `Practice.h`와 `Practice.cpp`가 있습니다. 이 두 파일에 한 줄씩 추가합니다.

```cpp
// Practice.h
#pragma once

#include "CoreMinimal.h"

DECLARE_LOG_CATEGORY_EXTERN(PracticeLog, Log, All);
```

```cpp
// Practice.cpp 의 #include 아래
DEFINE_LOG_CATEGORY(PracticeLog);
```

**코드 해석**

- `DECLARE_LOG_CATEGORY_EXTERN`은 카테고리를 선언합니다. 괄호 안은 순서대로 카테고리 이름, 실행 중 기본 상세도, 컴파일에 포함할 최대 상세도입니다.
- 두 번째 값 `Log`는 `Log`보다 중요한 로그만 기본으로 보이게 한다는 뜻입니다. `Verbose` 이하는 따로 켜야 보입니다.
- `DEFINE_LOG_CATEGORY`는 선언한 카테고리의 실체를 만듭니다. `.cpp` 파일 한 곳에만 적습니다.

카테고리를 쓰려는 파일에서 `#include "Practice.h"`를 추가하면 `UE_LOG(PracticeLog, Warning, TEXT("..."))`처럼 쓸 수 있습니다.

### 16-4 호출 위치를 찍는 로그 매크로

로그가 많아지면 "이 줄은 어느 함수에서 찍힌 것인가"를 알기 어려워집니다. 함수 이름과 줄 번호를 자동으로 붙여 주는 매크로를 만들어 둡니다. `Practice.h`의 카테고리 선언 아래에 추가합니다.

```cpp
// Practice.h
#define PLOG_CALLINFO (FString(__FUNCTION__) + TEXT("(") + FString::FromInt(__LINE__) + TEXT(")"))

#define PLOG_S(Verbosity) UE_LOG(PracticeLog, Verbosity, TEXT("%s"), *PLOG_CALLINFO)

#define PLOG(Verbosity, Format, ...) UE_LOG(PracticeLog, Verbosity, TEXT("%s %s"), *PLOG_CALLINFO, *FString::Printf(Format, ##__VA_ARGS__))
```

`MyActor.cpp`에서 써 봅니다.

```cpp
#include "Practice.h"

void AMyActor::BeginPlay()
{
	Super::BeginPlay();

	PLOG_S(Warning);
	PLOG(Warning, TEXT("Power = %f"), Power);
}
```

```text
PracticeLog: Warning: AMyActor::BeginPlay(18)
PracticeLog: Warning: AMyActor::BeginPlay(19) Power = 10.000000
```

**코드 해석**

- `__FUNCTION__`과 `__LINE__`은 컴파일러가 채워 주는 현재 함수 이름과 줄 번호입니다.
- `PLOG_S`는 위치만 찍습니다. 이 함수가 호출되는지만 확인할 때 씁니다.
- `PLOG`는 위치와 함께 내용을 찍습니다. `FString::Printf`는 화면에 출력하지 않고 형식에 맞춘 문자열을 만들어 돌려주는 함수입니다.

`PLOG`라는 이름은 원하는 이름으로 바꿔도 됩니다. 이후 장에서 디버깅할 때 이 매크로를 계속 씁니다. 괄호 안의 줄 번호는 코드 위치에 따라 달라집니다.

### 16-5 게임모드

언리얼은 "미리 만든 레벨에 규칙을 부여하고, 그 안에서 움직일 액터를 배치한다"는 틀로 게임을 만듭니다. 이때 규칙을 담당하는 액터가 **게임모드**입니다.

게임모드가 정하는 것의 예입니다.

- 플레이어가 게임에 들어오는 방식과 처음 나타나는 위치
- 플레이어 최대 인원
- 승리와 패배 조건
- 게임을 일시 정지할 수 있는지

게임모드의 부모 클래스는 두 가지입니다.

| 클래스 | 특징 |
|---|---|
| `AGameModeBase` | 가장 기본이 되는 게임모드. 대부분의 게임에 쓴다 |
| `AGameMode` | `AGameModeBase`를 상속받아 대기·진행·종료 같은 경기 상태 관리를 더한 것. 대전 게임에 쓴다 |

처음에는 `AGameModeBase`로 시작합니다.

### 16-6 게임모드가 지정하는 클래스

게임모드는 규칙과 함께 "이 게임에서 쓸 주요 클래스가 무엇인가"도 정합니다. Chapter 01에서 바꿔 본 디폴트 폰 클래스가 그중 하나입니다.

![월드 세팅의 게임모드 항목: 디폴트 폰 클래스·HUD 클래스·플레이어 컨트롤러 클래스·게임 스테이트 클래스·플레이어 스테이트 클래스·관전자 클래스 목록](./img/16-gamemode-classes.png)

| 항목 | 역할 |
|---|---|
| 디폴트 폰 클래스 | 게임 시작 시 플레이어에게 주어지는 폰 |
| 플레이어 컨트롤러 클래스 | 플레이어의 입력을 받아 폰을 조종하는 클래스 |
| HUD 클래스 | 화면 위에 정보를 그리는 예전 방식의 클래스 |
| 게임 스테이트 클래스 | 모든 플레이어가 함께 알아야 하는 경기 정보를 담는다 |
| 플레이어 스테이트 클래스 | 플레이어 한 명의 정보를 담는다 |
| 관전자 클래스 | 플레이하지 않고 지켜보는 사람용 폰 |

**폰**은 컨트롤러가 조종할 수 있는 액터입니다. 폰 중에서 사람처럼 걸어 다니는 것을 따로 **캐릭터**라고 부릅니다. 보드게임의 말처럼 눈에 보이는 모형이 없는 폰도 있습니다.

**컨트롤러**는 폰을 조종하는 주체입니다. 사람이 조종하면 플레이어 컨트롤러, 컴퓨터가 조종하면 AI 컨트롤러입니다. 컨트롤러가 폰을 조종할 수 있는 상태가 되는 것을 **빙의**라고 하고, 코드에서는 `Possess`라고 씁니다. 컨트롤러 하나는 한 번에 폰 하나만 조종합니다. 폰이 몸이라면 컨트롤러는 그 몸을 움직이는 정신에 해당합니다.

**게임 스테이트**와 **플레이어 스테이트**는 정보를 담는 곳입니다. 남은 시간이나 팀 점수처럼 모두에게 같은 정보는 게임 스테이트에, 내 처치 수처럼 플레이어마다 다른 정보는 플레이어 스테이트에 둡니다.

HUD 클래스는 언리얼 4부터 위젯 블루프린트 방식으로 대체되어 지금은 거의 쓰지 않습니다. 이 과정에서도 UI는 Chapter 22와 25에서 위젯 블루프린트로 만듭니다.

### 16-7 플레이어가 입장하는 순서

게임이 시작되어 플레이어 한 명이 들어오면 게임모드는 다음 순서로 준비합니다.

1. 플레이어 컨트롤러를 만든다.
2. 플레이어가 나타날 위치를 정한다. 레벨에 플레이어 스타트가 있으면 그 위치를 쓴다.
3. 디폴트 폰 클래스로 폰을 만든다.
4. 플레이어 컨트롤러가 폰에 빙의한다.
5. 입장 처리를 마친다.

레벨에 캐릭터를 직접 배치하지 않아도 플레이하면 캐릭터가 나타나는 이유가 이것입니다.

### 16-8 게임모드 만들어 적용하기

C++로 게임모드를 만들고 입장 순서를 로그로 확인합니다.

1. **툴 → 새 C++ 클래스**에서 부모로 **Game Mode Base**를 고르고 이름을 `MyGameMode`로 짓는다.
2. 헤더와 소스에 아래 코드를 넣는다.

```cpp
// MyGameMode.h 의 클래스 안
public:
	AMyGameMode();

	virtual void PostLogin(APlayerController* NewPlayer) override;
```

```cpp
// MyGameMode.cpp
#include "MyGameMode.h"
#include "Practice.h"
#include "GameFramework/DefaultPawn.h"

AMyGameMode::AMyGameMode()
{
	DefaultPawnClass = ADefaultPawn::StaticClass();
}

void AMyGameMode::PostLogin(APlayerController* NewPlayer)
{
	PLOG(Warning, TEXT("PostLogin Begin"));
	Super::PostLogin(NewPlayer);
	PLOG(Warning, TEXT("PostLogin End : %s"), *NewPlayer->GetPawn()->GetName());
}
```

**코드 해석**

- `DefaultPawnClass`가 디폴트 폰 클래스 항목에 해당하는 변수입니다.
- `클래스이름::StaticClass()`는 그 클래스 자체를 가리키는 정보를 돌려줍니다. 객체가 아니라 "어떤 클래스인지"를 넘길 때 씁니다.
- `ADefaultPawn`은 엔진이 제공하는 기본 폰입니다. 모형 없이 날아다니는 카메라 같은 폰입니다.
- `PostLogin`은 플레이어 입장 처리 과정에서 호출되는 함수입니다. `Super::PostLogin`이 끝난 시점에는 폰 생성과 빙의가 끝나 있으므로 `GetPawn()`으로 폰을 가져올 수 있습니다.

3. 빌드한 뒤 에디터에서 **월드 세팅**을 연다.
4. **게임모드 오버라이드**를 `MyGameMode`로 바꾼다.
5. 플레이해서 출력 로그에 `PostLogin Begin`과 `PostLogin End`가 차례로 찍히는지 확인한다.

게임모드를 정하는 곳은 두 군데입니다.

| 위치 | 적용 범위 |
|---|---|
| **편집 → 프로젝트 세팅 → 맵 & 모드 → 기본 게임모드** | 프로젝트의 모든 레벨 |
| **월드 세팅 → 게임모드 오버라이드** | 지금 연 레벨만 |

월드 세팅에 값이 있으면 그 레벨에서는 프로젝트 세팅의 값보다 우선합니다.

### 16-9 따라하기 — 로그와 게임모드

1. `MyActor`의 `BeginPlay`에 `UE_LOG`로 정수, 실수, 문자열을 하나씩 찍는다.
2. `PracticeLog` 카테고리와 `PLOG` 매크로를 만든다.
3. `FloatingActor`의 `BeginPlay`에 `PLOG_S(Warning);`을 넣어 호출 위치가 찍히는지 확인한다.
4. Chapter 14의 컨테이너 예제에서 `Total`과 `RifleAmmo` 값을 `PLOG`로 찍어 확인한다.
5. `MyGameMode`를 만들어 레벨에 적용하고 입장 로그를 확인한다.
6. `MyGameMode`를 부모로 블루프린트 `BP_MyGameMode`를 만들어 월드 세팅에 지정한다. 디폴트 폰 클래스를 `SpectatorPawn`으로 바꾸고, 플레이 시 `PostLogin End` 로그에 찍히는 폰 이름이 달라지는지 확인한다.

월드 세팅의 디폴트 폰 클래스 칸은 게임모드가 블루프린트일 때만 고칠 수 있습니다. C++ 게임모드를 직접 지정하면 회색으로 잠겨 있습니다. 규칙은 C++에 두고, 어떤 클래스를 쓸지는 블루프린트 자식에서 고르는 구조가 여기서도 쓰입니다.

### 연습문제

1. 문제: `FString` 변수 `Name`과 정수 변수 `Count`를 한 줄에 찍는 `UE_LOG` 문장을 쓰세요.  
   - 조건: 카테고리는 `LogTemp`, 상세도는 `Warning`
2. 문제: 플레이어 한 명의 처치 수와 경기의 남은 시간은 각각 어느 클래스에 두는 것이 맞는지 쓰세요.
3. 문제: 레벨에 캐릭터를 배치하지 않았는데 플레이하면 캐릭터가 나타나는 이유를 한 문장으로 쓰세요.  
   - 조건: 디폴트 폰 클래스를 언급할 것

### 정답 포인트

- `UE_LOG(LogTemp, Warning, TEXT("%s %d"), *Name, Count);`. 처치 수는 플레이어 스테이트, 남은 시간은 게임 스테이트. 게임모드가 입장 과정에서 디폴트 폰 클래스로 폰을 만들어 빙의시키기 때문.

---

[상위 문서로 돌아가기](./README.md)
