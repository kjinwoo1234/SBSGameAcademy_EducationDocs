# Chapter 22 데미지와 게임 규칙

## 학습 목표

- `ApplyDamage`와 `TakeDamage`로 데미지를 주고받는 흐름을 구현할 수 있다.
- 위젯 블루프린트로 체력을 화면에 표시할 수 있다.
- `PlayerState`에 처치 점수를 기록할 수 있다.
- `GameMode`에서 목표 점수 도달 시 게임을 다시 시작하는 규칙을 만들 수 있다.

## 본문

### 22-1 데미지 프레임워크

Chapter 21에서 공격이 누구에게 맞았는지 알아냈습니다. 이제 맞은 쪽의 체력을 깎아야 합니다. 맞은 액터의 체력 변수를 공격한 쪽에서 직접 고칠 수도 있지만, 그러면 공격하는 코드가 상대의 클래스와 변수 이름을 전부 알아야 합니다. 상자, 문, 적처럼 맞을 수 있는 대상이 늘어날 때마다 공격 코드를 고쳐야 합니다.

언리얼은 모든 액터에 공통으로 쓰는 데미지 전달 통로를 제공합니다.

| 쪽 | 하는 일 | 함수 |
|---|---|---|
| 주는 쪽 | "이 액터에게 이만큼 피해를 줘"라고 요청한다 | `UGameplayStatics::ApplyDamage` |
| 받는 쪽 | 피해를 받았을 때 할 일을 정한다 | `AActor::TakeDamage`를 재정의 |

주는 쪽은 상대가 무엇인지 몰라도 되고, 받는 쪽은 자기 방식대로 반응하면 됩니다.

### 22-2 데미지 받기

`TakeDamage`는 모든 액터가 가진 가상 함수입니다.

| 매개변수 | 의미 |
|---|---|
| `DamageAmount` | 받은 피해량 |
| `DamageEvent` | 피해의 종류와 상세 정보를 담은 구조체 |
| `EventInstigator` | 피해를 일으킨 컨트롤러. 누가 공격했는가 |
| `DamageCauser` | 피해를 직접 입힌 액터. 무엇에 맞았는가 |
| 반환값 | 실제로 적용된 피해량 |

캐릭터에 체력을 추가하고 `TakeDamage`를 재정의합니다.

```cpp
// MyCharacter.h
public:
	virtual float TakeDamage(float DamageAmount, struct FDamageEvent const& DamageEvent,
		class AController* EventInstigator, AActor* DamageCauser) override;

private:
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Stat, meta = (AllowPrivateAccess = "true"))
	float MaxHealth = 100.0f;

	UPROPERTY(VisibleInstanceOnly, BlueprintReadOnly, Category = Stat, meta = (AllowPrivateAccess = "true"))
	float Health = 100.0f;
```

```cpp
// MyCharacter.cpp
float AMyCharacter::TakeDamage(float DamageAmount, FDamageEvent const& DamageEvent,
	AController* EventInstigator, AActor* DamageCauser)
{
	const float FinalDamage = Super::TakeDamage(DamageAmount, DamageEvent, EventInstigator, DamageCauser);

	Health = FMath::Clamp(Health - FinalDamage, 0.0f, MaxHealth);
	PLOG(Warning, TEXT("%s took damage %f, Health = %f"), *GetName(), FinalDamage, Health);

	if (Health <= 0.0f)
	{
		Destroy();
	}

	return FinalDamage;
}
```

**코드 해석**

- `Super::TakeDamage`를 먼저 호출해 엔진의 기본 처리를 거친 피해량을 받습니다.
- `FMath::Clamp`로 체력이 `0`보다 작아지지 않게 합니다.
- `Destroy()`는 액터를 레벨에서 없애는 함수입니다.
- 실제로 적용한 피해량을 돌려줍니다.

### 22-3 데미지 주기

Chapter 21의 `AttackCheck`에서 로그를 찍던 부분을 데미지 요청으로 바꿉니다.

```cpp
// MyCharacter.cpp
#include "Kismet/GameplayStatics.h"

// AttackCheck 함수 안
if (bResult && HitResult.GetActor() != nullptr)
{
	UGameplayStatics::ApplyDamage(
		HitResult.GetActor(),
		50.0f,
		GetController(),
		this,
		UDamageType::StaticClass());
}
```

**코드 해석**

- `ApplyDamage(받을 액터, 피해량, 공격한 컨트롤러, 피해를 입힌 액터, 데미지 타입 클래스)` 형태입니다.
- `GetController()`는 이 캐릭터를 조종하는 컨트롤러입니다. 받는 쪽의 `EventInstigator`로 전달됩니다.
- `UDamageType`은 불, 독처럼 피해의 종류를 구분할 때 쓰는 클래스입니다. 구분이 필요 없으면 기본 클래스를 넘깁니다.

빌드하고 레벨에 놓인 캐릭터를 두 번 공격하면 로그에 체력이 `50`, `0`으로 찍히고 캐릭터가 사라집니다.

한 점에 주는 피해와 폭발처럼 범위에 주는 피해를 구분하고 싶을 때는 `ApplyDamage` 대신 `ApplyPointDamage`나 `ApplyRadialDamage`를 씁니다. 받는 쪽에서는 `DamageEvent.IsOfType(FPointDamageEvent::ClassID)`로 어떤 종류인지 확인해 맞은 위치 같은 추가 정보를 꺼낼 수 있습니다. 이 타입들을 쓰려면 `#include "Engine/DamageEvents.h"`가 필요합니다.

### 22-4 위젯 블루프린트로 체력 표시

체력을 로그가 아니라 화면에 보여 줍니다. 버튼, 글자, 체력바처럼 화면 위에 그려지는 요소를 통틀어 **UI**라고 합니다. 언리얼에서 UI를 만드는 도구가 **UMG**이고, UMG로 만든 에셋이 **위젯 블루프린트**입니다. 위젯은 UI를 이루는 부품 하나하나를 가리킵니다.

1. 콘텐츠 브라우저를 우클릭해 **유저 인터페이스 → 위젯 블루프린트**를 누른다.
2. 부모 클래스로 **User Widget**을 고르고 이름을 `WBP_HUD`로 짓는다.
3. 더블클릭해 연다.

위젯 블루프린트 에디터는 오른쪽 위에 **디자이너**와 **그래프** 두 탭이 있습니다. 디자이너는 화면 배치를 만드는 곳이고, 그래프는 일반 블루프린트처럼 동작을 만드는 곳입니다.

4. 디자이너의 왼쪽 **팔레트**에서 **캔버스 패널**을 가운데로 끌어다 놓는다.
5. **텍스트**를 캔버스 패널 위로 끌어다 놓고 화면 왼쪽 위에 배치한다.
6. 텍스트를 선택하고 디테일 패널 맨 위에서 이름을 `HealthText`로 바꾼 뒤 **변수 여부**에 체크한다.

![위젯 블루프린트 디자이너: 왼쪽 팔레트와 계층구조, 가운데 캔버스에 배치된 HealthText, 오른쪽 디테일 패널의 변수 여부 체크](./img/22-widget-designer-health-text.png)

**변수 여부**에 체크해야 그래프에서 이 텍스트를 변수로 꺼내 쓸 수 있습니다.

그래프 탭에서 체력을 읽어 글자로 넣습니다.

7. `Event Tick`의 실행 핀을 드래그해 둔다.
8. `Get Owning Player Pawn` 노드를 만든다.
9. 그 출력을 캐릭터 클래스로 캐스트하는 노드에 연결한다. 템플릿 캐릭터 클래스 이름으로 `Cast To`를 검색한다.
10. 캐스트 결과에서 `Health`를 꺼낸다.
11. 변수 `HealthText`를 그래프로 끌어다 놓고 그 핀에서 `SetText (Text)` 노드를 만든다.
12. `Health`를 `SetText`의 **In Text**에 연결한다. 숫자를 글자로 바꾸는 변환 노드가 자동으로 끼어든다.
13. 실행 핀을 Tick → 캐스트 → SetText 순서로 연결하고 컴파일한다.

`Get Owning Player Pawn`은 이 위젯을 보고 있는 플레이어가 조종하는 폰을 가져옵니다. `Health`가 `BlueprintReadOnly`로 선언되어 있어서 블루프린트에서 읽을 수 있습니다.

만든 위젯을 화면에 올립니다. `BP_ThirdPersonCharacter`의 이벤트 그래프에 추가합니다.

14. `Event BeginPlay`에 `Is Locally Controlled` 노드와 `Branch` 노드를 연결한다.
15. `Branch`의 **True**에 `Create Widget` 노드를 연결하고 **Class**를 `WBP_HUD`로 고른다.
16. `Create Widget`의 **Return Value**에서 `Add to Viewport` 노드를 만들어 연결한다.

**Branch**는 조건이 참인지 거짓인지에 따라 실행을 나누는 노드로 C++의 `if`와 같습니다. **Is Locally Controlled**는 이 캐릭터를 이 컴퓨터의 플레이어가 조종하고 있을 때만 참입니다. 레벨에 놓아 둔 다른 캐릭터까지 위젯을 만들지 않게 막아 줍니다. 위젯을 더 다양하게 꾸미는 방법은 Chapter 25에서 다룹니다.

### 22-5 PlayerState에 처치 점수 기록

적을 처치할 때마다 점수를 올립니다. 점수를 캐릭터에 두면 캐릭터가 죽어 사라질 때 점수도 함께 사라집니다. Chapter 16에서 배운 대로 플레이어 한 명의 정보는 **플레이어 스테이트**에 둡니다.

1. **툴 → 새 C++ 클래스**에서 부모로 `PlayerState`를 고르고 이름을 `MyPlayerState`로 짓는다.

```cpp
// MyPlayerState.h 의 클래스 안
public:
	void AddKillScore();
	int32 GetKillScore() const { return KillScore; }

	virtual void Reset() override;

private:
	UPROPERTY(VisibleInstanceOnly, BlueprintReadOnly, Category = Score, meta = (AllowPrivateAccess = "true"))
	int32 KillScore = 0;
```

```cpp
// MyPlayerState.cpp
void AMyPlayerState::AddKillScore()
{
	KillScore++;
}

void AMyPlayerState::Reset()
{
	Super::Reset();
	KillScore = 0;
}
```

`Reset`은 게임이 초기화될 때 엔진이 호출하는 함수입니다. 점수를 `0`으로 되돌립니다.

캐릭터가 쓰러질 때 공격한 플레이어의 점수를 올립니다. `TakeDamage`의 사망 처리 부분을 고칩니다.

```cpp
// MyCharacter.cpp
#include "MyPlayerState.h"

// TakeDamage 함수 안
if (Health <= 0.0f)
{
	if (EventInstigator != nullptr)
	{
		AMyPlayerState* KillerState = EventInstigator->GetPlayerState<AMyPlayerState>();
		if (KillerState != nullptr)
		{
			KillerState->AddKillScore();
		}
	}

	Destroy();
}
```

`EventInstigator`는 공격한 쪽의 컨트롤러이고, `GetPlayerState<타입>()`으로 그 플레이어의 플레이어 스테이트를 가져옵니다.

마지막으로 게임모드가 이 플레이어 스테이트 클래스를 쓰도록 지정합니다. Chapter 16에서 만든 `MyGameMode`의 생성자에 추가합니다.

```cpp
// MyGameMode.cpp
#include "MyPlayerState.h"

// 생성자 안
PlayerStateClass = AMyPlayerState::StaticClass();
```

`MyGameMode`의 `DefaultPawnClass`는 3인칭 캐릭터 블루프린트를 쓰도록 17-2의 `FClassFinder` 코드로 바꾸고, 레벨의 월드 세팅에서 게임모드 오버라이드를 `MyGameMode`로 지정합니다.

### 22-6 GameMode에서 승리 조건 확인

"처치 점수가 10점이 되면 게임을 다시 시작한다"는 규칙을 만듭니다. 규칙은 게임모드의 일입니다.

먼저 간단한 방법으로, 매 프레임 모든 플레이어의 점수를 확인합니다.

```cpp
// MyGameMode.h 의 클래스 안
public:
	virtual void Tick(float DeltaTime) override;

private:
	UPROPERTY(EditAnywhere, Category = Rule)
	int32 MaxKillScore = 10;
```

```cpp
// MyGameMode.cpp
#include "GameFramework/GameStateBase.h"
#include "Kismet/GameplayStatics.h"

// 생성자 안에 추가
PrimaryActorTick.bCanEverTick = true;

void AMyGameMode::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);

	for (APlayerState* PlayerState : GameState->PlayerArray)
	{
		AMyPlayerState* MyPlayerState = Cast<AMyPlayerState>(PlayerState);
		if (MyPlayerState != nullptr && MyPlayerState->GetKillScore() >= MaxKillScore)
		{
			MyPlayerState->Reset();
			UGameplayStatics::OpenLevel(this, FName(*UGameplayStatics::GetCurrentLevelName(this)));
			return;
		}
	}
}
```

**코드 해석**

- `GameState->PlayerArray`는 게임에 참가한 모든 플레이어의 플레이어 스테이트가 담긴 `TArray`입니다.
- `OpenLevel(기준 객체, 레벨 이름)`은 레벨을 엽니다. 지금 레벨의 이름을 넘기면 같은 레벨을 처음부터 다시 엽니다.

테스트를 위해 `MaxKillScore`를 `2`로 낮추고, 레벨에 캐릭터를 여러 개 놓은 뒤 처치해 봅니다.

### 22-7 델리게이트로 고치기

위 방법은 아무 일이 없어도 매 프레임 점수를 확인합니다. 점수는 누군가 쓰러질 때만 바뀌므로, 그때만 확인하면 됩니다. Chapter 20에서 배운 델리게이트로 "캐릭터가 쓰러졌다"는 사실을 게임모드에 알립니다.

캐릭터에 델리게이트를 만듭니다.

```cpp
// MyCharacter.h — UCLASS() 위
DECLARE_MULTICAST_DELEGATE_OneParam(FOnCharacterDeadDelegate, AController* /*Killer*/);

// 클래스 안
public:
	FOnCharacterDeadDelegate OnCharacterDead;
```

`DECLARE_MULTICAST_DELEGATE_OneParam`은 매개변수 하나를 함께 전달하는 델리게이트 타입을 만듭니다. 타입 이름 다음에 매개변수의 타입을 적습니다.

`TakeDamage`의 사망 처리에서 방송합니다.

```cpp
if (Health <= 0.0f)
{
	OnCharacterDead.Broadcast(EventInstigator);
	Destroy();
}
```

게임모드에 등록 함수와 처리 함수를 만듭니다.

```cpp
// MyGameMode.h 의 클래스 안
public:
	void RegisterCharacter(class AMyCharacter* Character);

private:
	void OnCharacterDead(AController* Killer);
```

```cpp
// MyGameMode.cpp
#include "MyCharacter.h"

void AMyGameMode::RegisterCharacter(AMyCharacter* Character)
{
	if (Character != nullptr)
	{
		Character->OnCharacterDead.AddUObject(this, &AMyGameMode::OnCharacterDead);
	}
}

void AMyGameMode::OnCharacterDead(AController* Killer)
{
	if (Killer == nullptr)
	{
		return;
	}

	AMyPlayerState* KillerState = Killer->GetPlayerState<AMyPlayerState>();
	if (KillerState == nullptr)
	{
		return;
	}

	KillerState->AddKillScore();

	if (KillerState->GetKillScore() >= MaxKillScore)
	{
		KillerState->Reset();
		UGameplayStatics::OpenLevel(this, FName(*UGameplayStatics::GetCurrentLevelName(this)));
	}
}
```

캐릭터는 게임이 시작될 때 게임모드에 자신을 등록합니다.

```cpp
// MyCharacter.cpp
#include "MyGameMode.h"

// BeginPlay 함수 안
AMyGameMode* GameMode = Cast<AMyGameMode>(GetWorld()->GetAuthGameMode());
if (GameMode != nullptr)
{
	GameMode->RegisterCharacter(this);
}
```

`GetWorld()->GetAuthGameMode()`는 지금 레벨의 게임모드를 가져옵니다.

이제 게임모드의 `Tick` 함수와 `PrimaryActorTick.bCanEverTick = true;`는 지우고, 캐릭터의 `TakeDamage`에 있던 점수 올리는 코드도 지웁니다. 점수 올리기와 승리 확인이 모두 게임모드의 `OnCharacterDead` 한 곳으로 모였습니다. 캐릭터는 "쓰러졌다"고 알리기만 하고, 그 뒤에 무슨 규칙이 적용되는지는 모릅니다.

### 22-8 따라하기 — 처치 점수 게임

1. 캐릭터에 `Health`와 `TakeDamage`를 추가한다.
2. `AttackCheck`에서 `ApplyDamage`를 호출해 레벨의 캐릭터가 쓰러지는지 확인한다.
3. `WBP_HUD`를 만들어 내 체력을 화면에 표시한다.
4. `MyPlayerState`를 만들어 처치 점수를 기록한다.
5. `MyGameMode`에서 목표 점수 도달 시 레벨을 다시 열게 한다.
6. 승리 확인을 `Tick` 방식에서 델리게이트 방식으로 바꾼다.
7. `WBP_HUD`에 텍스트를 하나 더 추가해 처치 점수를 표시한다. `Get Owning Player`에서 `Player State`를 꺼내 `MyPlayerState`로 캐스트한 뒤 `Kill Score`를 읽는다.

### 연습문제

1. 문제: 공격하는 쪽이 상대의 체력 변수를 직접 고치지 않고 `ApplyDamage`를 쓰는 이유를 한 문장으로 쓰세요.
2. 문제: 처치 점수를 캐릭터가 아니라 플레이어 스테이트에 두는 이유를 한 문장으로 쓰세요.  
   - 조건: 캐릭터가 `Destroy`되는 상황을 언급할 것
3. 문제: 승리 조건을 게임모드의 `Tick`에서 확인하는 방법과 델리게이트로 확인하는 방법의 차이를 두 문장으로 쓰세요.

### 정답 포인트

- 상대의 클래스와 변수를 몰라도 모든 액터에 같은 방식으로 피해를 줄 수 있어서. 캐릭터가 사라지면 캐릭터에 둔 점수도 사라지기 때문. `Tick`은 매 프레임 확인하고, 델리게이트는 캐릭터가 쓰러진 순간에만 확인한다.

---

[상위 문서로 돌아가기](./README.md)
