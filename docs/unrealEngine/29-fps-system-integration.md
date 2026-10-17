# Chapter 29 FPS 시스템 통합

## 학습 목표

- 체력을 컴포넌트로 분리해 여러 액터가 함께 쓰게 만들 수 있다.
- 닿으면 데미지를 주거나 회복시키는 오브젝트를 만들 수 있다.
- 사운드와 이펙트를 전투 이벤트에 붙여 재생할 수 있다.
- 시작부터 결과 화면, 재시작까지 한 판의 흐름을 완성할 수 있다.

## 본문

### 29-1 체력 컴포넌트

Chapter 22에서 체력을 캐릭터 클래스에 넣었습니다. Chapter 28에서 정한 대로 체력을 컴포넌트로 옮겨 플레이어, 적, 부서지는 상자가 같은 코드를 쓰게 합니다.

**툴 → 새 C++ 클래스**로 부모가 **액터 컴포넌트**인 `HealthComponent`를 만듭니다.

```cpp
// HealthComponent.h — UCLASS() 위
DECLARE_DYNAMIC_MULTICAST_DELEGATE_TwoParams(FOnHealthChangedSignature, float, Current, float, Max);
DECLARE_DYNAMIC_MULTICAST_DELEGATE(FOnDeathSignature);

// 클래스 안
public:
	void ReduceHealth(float Amount);
	void Heal(float Amount);

	float GetHealthPercent() const { return CurrentHealth / MaxHealth; }

	UPROPERTY(BlueprintAssignable, Category = Health)
	FOnHealthChangedSignature OnHealthChanged;

	UPROPERTY(BlueprintAssignable, Category = Health)
	FOnDeathSignature OnDeath;

protected:
	virtual void BeginPlay() override;

private:
	UPROPERTY(EditAnywhere, Category = Health)
	float MaxHealth = 100.0f;

	UPROPERTY(VisibleInstanceOnly, Category = Health)
	float CurrentHealth = 100.0f;
```

```cpp
// HealthComponent.cpp
void UHealthComponent::BeginPlay()
{
	Super::BeginPlay();
	CurrentHealth = MaxHealth;
}

void UHealthComponent::ReduceHealth(float Amount)
{
	if (Amount <= 0.0f || CurrentHealth <= 0.0f)
	{
		return;
	}

	CurrentHealth = FMath::Clamp(CurrentHealth - Amount, 0.0f, MaxHealth);
	OnHealthChanged.Broadcast(CurrentHealth, MaxHealth);

	if (CurrentHealth <= 0.0f)
	{
		OnDeath.Broadcast();
	}
}

void UHealthComponent::Heal(float Amount)
{
	if (Amount <= 0.0f || CurrentHealth <= 0.0f)
	{
		return;
	}

	CurrentHealth = FMath::Clamp(CurrentHealth + Amount, 0.0f, MaxHealth);
	OnHealthChanged.Broadcast(CurrentHealth, MaxHealth);
}
```

**코드 해석**

- 피해와 회복이 각각 함수 하나로 모였습니다. 체력을 바꾸는 길이 이 두 함수뿐이므로, UI 갱신 방송을 빠뜨릴 일이 없습니다.
- 첫 `if`는 잘못된 값과 이미 쓰러진 상태를 걸러 냅니다. 쓰러진 뒤에는 회복도 되지 않습니다.
- 체력이 `0`이 되면 `OnDeath`를 방송합니다. 쓰러졌을 때 무엇을 할지는 컴포넌트가 아니라 이 컴포넌트를 가진 액터가 정합니다.

캐릭터가 컴포넌트를 쓰도록 바꿉니다.

```cpp
// MyCharacter.h
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Stat, meta = (AllowPrivateAccess = "true"))
class UHealthComponent* HealthComponent;

UFUNCTION()
void HandleDeath();
```

```cpp
// MyCharacter.cpp
#include "HealthComponent.h"

// 생성자
HealthComponent = CreateDefaultSubobject<UHealthComponent>(TEXT("HealthComponent"));

// BeginPlay
HealthComponent->OnDeath.AddDynamic(this, &AMyCharacter::HandleDeath);

float AMyCharacter::TakeDamage(float DamageAmount, FDamageEvent const& DamageEvent,
	AController* EventInstigator, AActor* DamageCauser)
{
	const float FinalDamage = Super::TakeDamage(DamageAmount, DamageEvent, EventInstigator, DamageCauser);

	LastDamageInstigator = EventInstigator;
	HealthComponent->ReduceHealth(FinalDamage);

	return FinalDamage;
}

void AMyCharacter::HandleDeath()
{
	OnCharacterDead.Broadcast(LastDamageInstigator);
	Destroy();
}
```

`LastDamageInstigator`는 `AController*` 타입의 멤버 변수로 헤더에 `UPROPERTY()`와 함께 추가합니다. 마지막으로 공격한 컨트롤러를 기억해 두었다가 쓰러질 때 Chapter 22의 `OnCharacterDead`로 전달합니다. 캐릭터에 있던 `Health`, `MaxHealth`, `OnHealthChanged`는 지우고, Chapter 25의 체력바 위젯은 캐릭터 대신 `HealthComponent`의 `OnHealthChanged`에 등록하도록 고칩니다.

### 29-2 데미지 오브젝트와 회복 아이템

레벨에 놓아 두는 함정과 회복 아이템을 만듭니다. Chapter 12의 오버랩 감지를 C++로 옮긴 것입니다. 부모가 액터인 `DamageVolume` 클래스를 만듭니다.

```cpp
// DamageVolume.h 의 클래스 안
private:
	UFUNCTION()
	void OnBeginOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor,
		UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult);

	UPROPERTY(VisibleAnywhere)
	class UBoxComponent* Box;

	UPROPERTY(EditAnywhere, Category = Damage)
	float Amount = 30.0f;

	UPROPERTY(EditAnywhere, Category = Damage)
	bool bIsHeal = false;
```

```cpp
// DamageVolume.cpp
#include "Components/BoxComponent.h"
#include "HealthComponent.h"
#include "Kismet/GameplayStatics.h"

ADamageVolume::ADamageVolume()
{
	Box = CreateDefaultSubobject<UBoxComponent>(TEXT("Box"));
	Box->SetCollisionProfileName(TEXT("OverlapAllDynamic"));
	RootComponent = Box;
}

void ADamageVolume::BeginPlay()
{
	Super::BeginPlay();
	Box->OnComponentBeginOverlap.AddDynamic(this, &ADamageVolume::OnBeginOverlap);
}

void ADamageVolume::OnBeginOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor,
	UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult)
{
	if (OtherActor == nullptr)
	{
		return;
	}

	if (bIsHeal)
	{
		UHealthComponent* Health = OtherActor->FindComponentByClass<UHealthComponent>();
		if (Health != nullptr)
		{
			Health->Heal(Amount);
			Destroy();
		}
	}
	else
	{
		UGameplayStatics::ApplyDamage(OtherActor, Amount, nullptr, this, UDamageType::StaticClass());
	}
}
```

**코드 해석**

- `OnComponentBeginOverlap`은 블루프린트의 On Component Begin Overlap과 같은 델리게이트입니다. 등록하는 함수의 매개변수 여섯 개는 엔진이 정한 형태 그대로 적습니다.
- `OverlapAllDynamic`은 모든 것과 오버랩으로 반응하는 엔진 프리셋입니다.
- `FindComponentByClass<타입>()`은 액터에 그 종류의 컴포넌트가 붙어 있으면 가져옵니다. 상대가 캐릭터인지 상자인지 몰라도 체력 컴포넌트만 있으면 회복시킬 수 있습니다.
- 회복 아이템은 한 번 쓰면 `Destroy`로 사라지고, 함정은 남아 있습니다.

이 클래스는 플레이어의 입력이나 캐릭터 코드와 섞이지 않습니다. 맵에 놓이는 오브젝트의 책임은 그 오브젝트 안에 둡니다. `bIsHeal` 값이 다른 블루프린트 두 개를 만들어 함정과 회복 아이템으로 씁니다.

### 29-3 사운드와 이펙트

전투에 반응이 느껴지려면 이벤트마다 소리와 시각 효과가 따라와야 합니다.

| 이벤트 | 피드백 예 |
|---|---|
| 발사 | 총성, 총구 화염 |
| 명중 | 명중음, 불꽃 |
| 피격 | 피격음, 화면 깜빡임 |
| 처치 | 점수 UI 갱신, 짧은 효과음 |
| 승리·패배 | 결과 화면, 입력 정지 |

![발사 순간 총구 이펙트와 명중 지점의 불꽃 이펙트가 보이는 플레이 화면](./img/29-muzzle-impact-fx.png)

캐릭터 헤더에 에셋을 지정할 변수를 추가합니다.

```cpp
// MyCharacter.h
UPROPERTY(EditAnywhere, Category = Feedback)
class USoundBase* FireSound;

UPROPERTY(EditAnywhere, Category = Feedback)
class UNiagaraSystem* ImpactFX;
```

`Fire` 함수에서 재생합니다.

```cpp
// MyCharacter.cpp
#include "NiagaraFunctionLibrary.h"

if (FireSound != nullptr)
{
	UGameplayStatics::PlaySoundAtLocation(this, FireSound, GetActorLocation());
}

if (bHit && ImpactFX != nullptr)
{
	UNiagaraFunctionLibrary::SpawnSystemAtLocation(
		GetWorld(), ImpactFX, Hit.ImpactPoint, Hit.ImpactNormal.Rotation());
}
```

**코드 해석**

- `PlaySoundAtLocation(기준 객체, 사운드, 위치)`는 그 위치에서 소리를 한 번 재생합니다.
- **나이아가라**는 언리얼의 파티클 이펙트 시스템입니다. `SpawnSystemAtLocation`은 이펙트를 지정한 위치와 회전으로 한 번 재생합니다.
- `Hit.ImpactNormal`은 맞은 면이 바라보는 방향입니다. 이펙트가 벽에서 바깥쪽으로 튀게 합니다.

나이아가라 함수를 쓰려면 `Practice.Build.cs`의 모듈 목록에 `"Niagara"`를 추가해야 합니다. 사운드와 이펙트 에셋은 시작용 콘텐츠에 들어 있는 것이나 Fab의 무료 에셋을 캐릭터 블루프린트에서 지정합니다. 중요한 것은 어떤 에셋이냐가 아니라 **이벤트가 일어난 시점**에 재생을 호출하는 것입니다.

### 29-4 한 판의 흐름

Chapter 22의 게임모드는 목표 점수에 도달하면 바로 레벨을 다시 열었습니다. 결과를 보여 주고 플레이어가 다시 시작을 고르게 바꿉니다.

1. 위젯 블루프린트 `WBP_Result`를 만든다. 결과 문구 텍스트와 **다시 시작** 버튼을 놓는다.
2. 버튼의 **On Clicked** 이벤트에 `Open Level (by Name)`과 `Get Current Level Name`을 연결한다.
3. 게임모드에서 목표 점수 도달 시 `OpenLevel` 대신 결과 위젯을 띄운다.

```cpp
// MyGameMode.cpp — OnCharacterDead 의 승리 처리 부분
APlayerController* PC = Cast<APlayerController>(Killer);
if (PC != nullptr && ResultWidgetClass != nullptr)
{
	UUserWidget* Result = CreateWidget<UUserWidget>(PC, ResultWidgetClass);
	Result->AddToViewport();

	PC->SetShowMouseCursor(true);
	PC->SetInputMode(FInputModeUIOnly());
}
```

**코드 해석**

- `ResultWidgetClass`는 Chapter 25의 `HudClass`와 같은 방식으로 헤더에 `TSubclassOf<UUserWidget>`으로 선언합니다.
- `SetShowMouseCursor(true)`로 마우스 커서를 보이게 합니다.
- `SetInputMode(FInputModeUIOnly())`는 입력을 UI에만 보내 캐릭터가 더 움직이지 않게 합니다.

플레이어가 쓰러졌을 때도 같은 방법으로 패배 문구를 띄웁니다.

### 29-5 폴리싱 점검

기능이 다 들어간 뒤 거친 부분을 다듬는 일을 **폴리싱**이라고 합니다. 아래 다섯 가지를 직접 플레이하며 확인합니다.

1. 이동, 조준, 발사가 끊김 없이 한 흐름으로 동작하는가.
2. 적 처치, 점수 증가, 목표 달성, 결과 화면이 순서대로 이어지는가.
3. 발사와 명중에 소리와 이펙트가 붙어 있는가.
4. 체력이 0이 되었을 때 입력과 카메라가 이상해지지 않는가.
5. 다시 시작한 뒤 점수와 체력이 처음 값으로 돌아오는가.

### 29-6 따라하기 — FPS 미니 게임 완성

1. `HealthComponent`를 만들어 캐릭터의 체력을 옮긴다.
2. 체력바 위젯이 `HealthComponent`의 방송을 받도록 고친다.
3. `DamageVolume`으로 함정과 회복 아이템을 만들어 레벨에 놓는다.
4. 발사와 명중에 사운드와 이펙트를 붙인다.
5. 승리와 패배 시 결과 화면을 띄우고 다시 시작 버튼을 연결한다.
6. Chapter 23의 적을 여러 마리 놓고, 적이 플레이어에게 닿으면 데미지를 주게 한다.
7. 폴리싱 점검 다섯 항목을 통과시킨다.

이 장으로 혼자 플레이하는 FPS 미니 게임이 한 바퀴 돕니다. Part 04에서는 같은 게임을 여러 사람이 함께 플레이하도록 확장합니다.

### 연습문제

1. 문제: 체력을 캐릭터 클래스가 아니라 컴포넌트에 두면 좋은 점을 한 문장으로 쓰세요.  
   - 조건: 캐릭터가 아닌 액터의 예를 하나 들 것
2. 문제: 함정 오브젝트의 데미지 코드를 플레이어 캐릭터 클래스 안에 넣지 않는 이유를 한 문장으로 쓰세요.
3. 문제: 한 판이 끝나려면 필요한 요소 세 가지를 목록으로 쓰세요.

### 정답 포인트

- 부서지는 상자처럼 캐릭터가 아닌 액터에도 같은 체력 코드를 붙일 수 있다. 맵 오브젝트의 책임을 분리해야 다른 대상에도 다시 쓸 수 있다. 종료 조건, 결과를 알리는 화면, 재시작 방법.

---

[상위 문서로 돌아가기](./README.md)
