# Chapter 34 네트워크 역할·권한

## 학습 목표

- 오소리티, 오너, 롤이 각각 무엇을 가리키는지 구분할 수 있다.
- 로그로 액터의 롤을 찍어 호스트와 클라이언트에서 어떻게 다른지 확인할 수 있다.
- 게임모드, 게임 스테이트, 플레이어 스테이트, 플레이어 컨트롤러가 어느 컴퓨터에 존재하는지 설명할 수 있다.
- 모두가 알아야 하는 경기 정보를 게임 스테이트에 두고 복제할 수 있다.

## 본문

### 34-1 같은 캐릭터가 컴퓨터마다 다르다

두 명이 플레이하면 캐릭터는 두 개지만, 캐릭터 객체는 네 개입니다. 호스트의 컴퓨터에 두 개, 클라이언트의 컴퓨터에 두 개가 따로 있습니다. 같은 캐릭터라도 어느 컴퓨터에 있는 객체냐에 따라 해도 되는 일이 다릅니다.

Chapter 30과 32에서 이미 두 가지 기준을 썼습니다.

| 용어 | 묻는 것 | 확인하는 방법 |
|---|---|---|
| **오소리티** | 이 액터의 최종 권한이 이 컴퓨터에 있는가 | `HasAuthority()` |
| **오너** | 이 액터를 어느 플레이어의 연결이 소유하는가 | Server RPC가 전달되는지로 드러난다 |

이 장에서는 세 번째 기준인 **롤**을 더합니다. 롤은 "이 컴퓨터에서 이 액터가 맡은 역할"입니다.

| 롤 | 뜻 | 예 |
|---|---|---|
| `ROLE_Authority` | 최종 권한을 가진 원본 | 서버에 있는 모든 캐릭터 |
| `ROLE_AutonomousProxy` | 내가 직접 조종하는 복사본 | 클라이언트 화면의 내 캐릭터 |
| `ROLE_SimulatedProxy` | 서버가 알려 준 대로 따라 그리는 복사본 | 클라이언트 화면의 다른 사람 캐릭터 |

프록시는 대리인이라는 뜻입니다. 클라이언트에 있는 액터는 모두 서버에 있는 원본의 대리인입니다.

### 34-2 롤을 로그로 확인하기

캐릭터의 `BeginPlay`에 로그를 한 줄 넣습니다.

```cpp
// MyCharacter.cpp 의 BeginPlay 안
PLOG(Warning, TEXT("%s Local=%s Remote=%s"),
	*GetName(),
	*UEnum::GetValueAsString(GetLocalRole()),
	*UEnum::GetValueAsString(GetRemoteRole()));
```

**코드 해석**

- `GetLocalRole()`은 이 컴퓨터에서의 롤입니다.
- `GetRemoteRole()`은 연결 건너편 컴퓨터에서의 롤입니다.
- `UEnum::GetValueAsString`은 열거형 값을 `ROLE_Authority` 같은 글자로 바꿔 줍니다.

플레이어 수 2, 리슨 서버로 실행하면 로그가 네 줄 찍힙니다.

| 찍힌 창 | 캐릭터 | Local | Remote |
|---|---|---|---|
| 호스트 | 호스트의 캐릭터 | `ROLE_Authority` | `ROLE_SimulatedProxy` |
| 호스트 | 클라이언트의 캐릭터 | `ROLE_Authority` | `ROLE_AutonomousProxy` |
| 클라이언트 | 클라이언트의 캐릭터 | `ROLE_AutonomousProxy` | `ROLE_Authority` |
| 클라이언트 | 호스트의 캐릭터 | `ROLE_SimulatedProxy` | `ROLE_Authority` |

호스트 창에서는 모든 캐릭터가 오소리티입니다. 클라이언트 창에서는 내 캐릭터만 오토노머스 프록시이고 나머지는 시뮬레이티드 프록시입니다. `HasAuthority()`는 `GetLocalRole() == ROLE_Authority`를 줄여 쓴 함수입니다.

### 34-3 어떤 확인을 언제 쓰는가

코드에서 자주 쓰는 확인은 두 가지입니다.

| 확인 | 참이 되는 곳 | 쓰는 곳 |
|---|---|---|
| `HasAuthority()` | 서버 | 체력, 점수, 스폰처럼 결과를 바꾸는 코드 |
| `IsLocallyControlled()` | 그 폰을 직접 조종하는 컴퓨터 | 입력, 카메라, 내 화면에만 띄우는 UI |

둘은 서로 다른 질문입니다. 호스트의 캐릭터는 호스트 컴퓨터에서 둘 다 참이고, 클라이언트의 캐릭터는 서버에서 `HasAuthority()`만 참이며, 클라이언트 컴퓨터에서 `IsLocallyControlled()`만 참입니다.

"내 컴퓨터에서만 동작하는" 버그는 대부분 이 둘을 섞어 쓴 데서 나옵니다. 체력을 바꾸는 코드에 `IsLocallyControlled`를 쓰면 클라이언트가 체력을 바꾸게 되고, 위젯을 띄우는 코드에 `HasAuthority`를 쓰면 호스트 화면에만 위젯이 뜹니다.

### 34-4 게임 프레임워크 클래스가 있는 곳

Chapter 16에서 배운 게임 프레임워크 클래스들은 컴퓨터마다 있는 것과 없는 것이 다릅니다.

| 클래스 | 서버 | 내 클라이언트 | 다른 클라이언트 | 담는 것 |
|---|---|---|---|---|
| 게임모드 | 있다 | 없다 | 없다 | 규칙, 스폰, 승패 판정 |
| 게임 스테이트 | 있다 | 있다 | 있다 | 모두가 알아야 하는 경기 상태 |
| 플레이어 스테이트 | 모두 있다 | 모두 있다 | 모두 있다 | 플레이어별 이름과 점수 |
| 플레이어 컨트롤러 | 모두 있다 | 내 것만 있다 | 그 사람 것만 있다 | 입력, 그 플레이어의 UI |
| 폰 | 모두 있다 | 모두 있다 | 모두 있다 | 몸과 움직임 |

클라이언트에서 `GetWorld()->GetAuthGameMode()`를 호출하면 `nullptr`이 돌아옵니다. 게임모드는 서버에만 있기 때문입니다. Chapter 22에서 캐릭터가 게임모드에 자신을 등록할 때 `nullptr` 확인을 넣어 둔 덕분에 클라이언트에서 오류가 나지 않습니다.

클라이언트가 규칙에 관한 값을 읽어야 할 때는 게임모드가 그 값을 **게임 스테이트**에 적어 두고, 게임 스테이트가 복제해 줍니다. 게임모드는 규칙을 집행하는 심판이고, 게임 스테이트는 모두가 볼 수 있게 걸어 둔 전광판입니다.

다른 사람의 플레이어 컨트롤러는 내 컴퓨터에 없다는 점도 기억해 둡니다. 다른 플레이어의 정보를 읽으려면 컨트롤러가 아니라 플레이어 스테이트나 폰을 봅니다.

### 34-5 게임 스테이트에 남은 시간 두기

경기 남은 시간을 만들어 봅니다. 시간을 줄이는 것은 규칙이므로 게임모드가 하고, 값은 모두가 봐야 하므로 게임 스테이트에 둡니다.

**툴 → 새 C++ 클래스**로 부모가 **Game State Base**인 `MyGameState`를 만듭니다.

```cpp
// MyGameState.h 의 클래스 안
public:
	virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;

	void SetRemainingSeconds(int32 NewSeconds) { RemainingSeconds = NewSeconds; }

	UFUNCTION(BlueprintPure, Category = Match)
	int32 GetRemainingSeconds() const { return RemainingSeconds; }

private:
	UPROPERTY(Replicated)
	int32 RemainingSeconds = 0;
```

```cpp
// MyGameState.cpp
#include "MyGameState.h"
#include "Net/UnrealNetwork.h"

void AMyGameState::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);
	DOREPLIFETIME(AMyGameState, RemainingSeconds);
}
```

**코드 해석**

- 게임 스테이트 액터는 처음부터 복제가 켜져 있습니다. 변수에 `Replicated`를 붙이고 `DOREPLIFETIME`으로 등록하면 됩니다.
- `BlueprintPure`는 값을 읽기만 하는 함수를 블루프린트에 노출하는 지정자입니다. 실행 핀 없이 값 핀만 있는 노드가 됩니다. Chapter 36에서 위젯이 이 함수로 남은 시간을 읽습니다.
- `SetRemainingSeconds`는 서버의 게임모드만 호출합니다.

게임모드가 이 클래스를 쓰도록 생성자에 한 줄을 추가합니다.

```cpp
// MyGameMode.cpp
#include "MyGameState.h"

// 생성자 안
GameStateClass = AMyGameState::StaticClass();
```

### 34-6 타이머로 1초마다 줄이기

1초마다 값을 줄이려면 `Tick`에서 시간을 직접 세는 방법도 있지만, 엔진의 **타이머**를 쓰면 더 간단합니다. 타이머는 "몇 초 뒤에 이 함수를 불러 줘"라고 엔진에 맡겨 두는 기능입니다.

```cpp
// MyGameMode.h 의 클래스 안
protected:
	virtual void BeginPlay() override;

private:
	void TickMatchTime();

	FTimerHandle MatchTimerHandle;

	UPROPERTY(EditAnywhere, Category = Rule)
	int32 MatchSeconds = 120;
```

```cpp
// MyGameMode.cpp
void AMyGameMode::BeginPlay()
{
	Super::BeginPlay();

	AMyGameState* MyGameState = GetGameState<AMyGameState>();
	if (MyGameState != nullptr)
	{
		MyGameState->SetRemainingSeconds(MatchSeconds);
	}

	GetWorldTimerManager().SetTimer(MatchTimerHandle, this, &AMyGameMode::TickMatchTime, 1.0f, true);
}

void AMyGameMode::TickMatchTime()
{
	AMyGameState* MyGameState = GetGameState<AMyGameState>();
	if (MyGameState == nullptr)
	{
		return;
	}

	const int32 Remaining = MyGameState->GetRemainingSeconds() - 1;
	MyGameState->SetRemainingSeconds(Remaining);

	if (Remaining <= 0)
	{
		GetWorldTimerManager().ClearTimer(MatchTimerHandle);
		PLOG(Warning, TEXT("Time over"));
	}
}
```

**코드 해석**

- `GetGameState<타입>()`은 게임모드가 가진 게임 스테이트를 원하는 타입으로 가져옵니다.
- `SetTimer(핸들, 대상 객체, 함수 주소, 간격, 반복 여부)` 형태입니다. 1초마다 `TickMatchTime`을 반복해서 부릅니다.
- `FTimerHandle`은 맡겨 둔 타이머를 가리키는 표입니다. 나중에 멈출 때 이 표를 내밉니다.
- `ClearTimer(핸들)`로 타이머를 멈춥니다. 시간이 다 됐을 때 경기를 끝내는 처리는 Chapter 37에서 넣습니다.

게임모드의 `BeginPlay`는 서버에서만 실행됩니다. 게임모드가 서버에만 있기 때문에 가드를 따로 쓰지 않아도 됩니다. 값이 클라이언트까지 가는지 확인하기 위해 캐릭터의 `Tick`에 임시 로그를 넣습니다. 캐릭터 소스 파일 위쪽에 `#include "MyGameState.h"`를 추가합니다.

```cpp
// MyCharacter.cpp 의 Tick 안 — 확인 후 지운다
const AMyGameState* MyGameState = GetWorld()->GetGameState<AMyGameState>();
if (MyGameState != nullptr && IsLocallyControlled())
{
	PLOG(Warning, TEXT("Remaining = %d"), MyGameState->GetRemainingSeconds());
}
```

두 창의 로그에서 같은 숫자가 1초마다 줄어들면 성공입니다.

### 34-7 책임을 어디에 둘지 정하는 기준

기능을 새로 넣을 때마다 아래 표로 자리를 정합니다.

| 넣으려는 것 | 둘 곳 | 이유 |
|---|---|---|
| 점수 올리기, 승패 판정 | 게임모드 | 규칙은 서버만 집행한다 |
| 남은 시간, 경기가 끝났는지 | 게임 스테이트 | 모두가 같은 값을 봐야 한다 |
| 처치 점수, 플레이어 이름 | 플레이어 스테이트 | 플레이어마다 있고 남도 봐야 한다 |
| 체력, 탄약 | 폰이나 폰의 컴포넌트 | 몸에 붙은 값이다 |
| 체력바, 결과 화면 | 플레이어 컨트롤러가 띄우는 위젯 | 그 플레이어의 화면에만 필요하다 |

### 34-8 따라하기 — 역할별 책임 분리

1. 캐릭터의 `BeginPlay`에 롤 로그를 넣고 두 창에서 네 줄을 확인해 34-2의 표와 비교한다.
2. `MyGameState`를 만들고 게임모드의 `GameStateClass`로 지정한다.
3. 게임모드에 타이머를 넣어 `RemainingSeconds`가 1초마다 줄게 한다.
4. 두 창의 로그에서 남은 시간이 같은 값으로 줄어드는지 확인한 뒤 임시 로그를 지운다.
5. 클라이언트에서 `GetAuthGameMode()`를 호출해 `nullptr`인지 로그로 확인한다.
6. 프로젝트 코드에서 `GetAuthGameMode`를 검색해, 호출하는 곳마다 `nullptr` 확인이 있는지 점검한다.
7. 지금까지 만든 기능을 34-7의 표에 맞춰 분류하고, 자리가 맞지 않는 것이 있으면 옮긴다.

### 연습문제

1. 문제: 클라이언트 화면에서 다른 사람의 캐릭터는 어떤 롤인지 쓰고, 그 캐릭터에 입력을 직접 넣지 않는 이유를 한 문장으로 쓰세요.
2. 문제: 위젯을 띄우는 코드에 `HasAuthority()`를 조건으로 쓰면 생기는 문제를 한 문장으로 쓰세요.
3. 문제: 남은 시간을 게임모드의 변수로만 두면 클라이언트 UI에 표시할 수 없는 이유를 한 문장으로 쓰세요.
4. 문제: 다른 플레이어의 처치 점수를 읽으려면 그 플레이어의 컨트롤러와 플레이어 스테이트 중 무엇을 봐야 하는지 쓰고 이유를 한 문장으로 쓰세요.

### 정답 포인트

- 시뮬레이티드 프록시. 서버가 알려 준 움직임을 따라 그리는 복사본이라서. 호스트 화면에만 위젯이 뜨고 클라이언트 화면에는 뜨지 않는다. 게임모드는 서버에만 있어 클라이언트에서는 `nullptr`이다. 플레이어 스테이트. 다른 사람의 컨트롤러는 내 컴퓨터에 없다.

---

[상위 문서로 돌아가기](./README.md)
