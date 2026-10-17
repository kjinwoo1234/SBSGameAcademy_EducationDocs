# Chapter 27 발사체와 히트스캔

## 학습 목표

- 3인칭 캐릭터를 1인칭 시점으로 바꿀 수 있다.
- 발사체 방식과 히트스캔 방식의 차이를 설명할 수 있다.
- 라인 트레이스로 조준한 곳의 액터에 데미지를 줄 수 있다.
- 발사체 액터를 만들어 스폰하고 명중 시 데미지를 줄 수 있다.

## 본문

### 27-1 1인칭 시점으로 바꾸기

Part 02에서는 검으로 근접 공격하는 3인칭 캐릭터를 만들었습니다. Part 03에서는 같은 프로젝트에 총을 쏘는 기능을 더해 1인칭 슈팅 게임의 뼈대를 만듭니다. 1인칭 슈팅 게임을 줄여서 FPS라고 부릅니다.

먼저 카메라를 캐릭터의 눈 위치로 옮깁니다. Chapter 17에서 읽은 생성자의 값을 바꿉니다.

```cpp
// MyCharacter.cpp 생성자
bUseControllerRotationYaw = true;
GetCharacterMovement()->bOrientRotationToMovement = false;

CameraBoom->TargetArmLength = 0.0f;
CameraBoom->SetRelativeLocation(FVector(0.0f, 0.0f, 70.0f));
```

**코드 해석**

- `bUseControllerRotationYaw = true`는 마우스를 좌우로 움직이면 캐릭터 몸이 함께 돌게 합니다.
- `bOrientRotationToMovement = false`는 이동 방향으로 몸이 도는 기능을 끕니다. 옆으로 걸어도 정면을 계속 봅니다.
- 스프링 암 길이를 `0`으로 하면 카메라가 캐릭터 위치에 붙습니다. 상대 위치의 Z를 올려 눈높이로 맞춥니다.

블루프린트에서 같은 값을 덮어쓰고 있으면 반영되지 않으므로 `BP_ThirdPersonCharacter`의 디테일 패널에서도 확인합니다. 플레이하면 자기 몸이 화면을 가릴 수 있습니다. 메시 컴포넌트의 **소유자 숨김**에 체크하면 조종하는 본인에게만 몸이 보이지 않습니다.

### 27-2 발사체와 히트스캔

총을 구현하는 방법은 크게 두 가지입니다.

| 방식 | 동작 | 잘 맞는 경우 |
|---|---|---|
| 발사체 | 총알 액터가 실제로 날아가다 부딪힌다 | 로켓, 화살, 느린 탄. 궤적이 보여야 할 때 |
| 히트스캔 | 쏘는 순간 직선을 그어 닿은 것을 바로 맞힌다 | 대부분의 총. 판정이 즉시여야 할 때 |

Chapter 26의 쇠구슬이 발사체 방식이었습니다. 발사체는 날아가는 시간이 있어 움직이는 적을 맞히려면 앞을 겨냥해야 하고, 히트스캔은 조준한 순간 결과가 정해집니다.

### 27-3 발사 입력

근접 공격이 마우스 왼쪽 버튼을 쓰고 있으므로 발사는 오른쪽 버튼에 연결합니다. Chapter 20의 공격 입력과 같은 순서입니다.

1. 입력 액션 `IA_Fire`를 만들고 `IMC_Default`에서 **오른쪽 마우스 버튼**에 연결한다.
2. 캐릭터 헤더에 추가한다.

```cpp
// MyCharacter.h
UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, meta = (AllowPrivateAccess = "true"))
UInputAction* FireAction;

void Fire();
FTransform GetMuzzleTransform() const;
```

3. `SetupPlayerInputComponent`에 바인딩을 추가한다.

```cpp
EnhancedInputComponent->BindAction(FireAction, ETriggerEvent::Started, this, &AMyCharacter::Fire);
```

4. 총알이 나갈 위치와 방향을 돌려주는 함수를 만든다.

```cpp
FTransform AMyCharacter::GetMuzzleTransform() const
{
	FVector ViewLocation;
	FRotator ViewRotation;
	GetActorEyesViewPoint(ViewLocation, ViewRotation);

	if (GetController() != nullptr)
	{
		ViewRotation = GetController()->GetControlRotation();
	}

	return FTransform(ViewRotation, ViewLocation + ViewRotation.Vector() * 50.0f);
}
```

**코드 해석**

- `GetActorEyesViewPoint`는 액터의 눈 위치와 바라보는 방향을 돌려줍니다.
- 방향은 컨트롤러가 바라보는 방향으로 바꿉니다. 위아래 조준이 반영됩니다.
- `ViewRotation.Vector()`는 그 회전이 가리키는 방향의 길이 1 벡터입니다. 눈에서 앞으로 `50`만큼 나간 곳을 총구로 삼습니다.
- `FTransform(회전, 위치)`로 두 값을 한 묶음으로 돌려줍니다.

빌드한 뒤 캐릭터 블루프린트에서 **Fire Action**에 `IA_Fire`를 지정합니다.

### 27-4 히트스캔 — 라인 트레이스

히트스캔은 **라인 트레이스**로 구현합니다. Chapter 21의 스윕이 구체를 쓸었다면, 라인 트레이스는 두께 없는 선을 긋습니다.

```cpp
// MyCharacter.cpp
void AMyCharacter::Fire()
{
	const FTransform Muzzle = GetMuzzleTransform();
	const FVector Start = Muzzle.GetLocation();
	const FVector End = Start + Muzzle.GetRotation().Vector() * 10000.0f;

	FHitResult Hit;
	FCollisionQueryParams Params;
	Params.AddIgnoredActor(this);

	const bool bHit = GetWorld()->LineTraceSingleByChannel(Hit, Start, End, ECC_Attack, Params);

#if ENABLE_DRAW_DEBUG
	DrawDebugLine(GetWorld(), Start, bHit ? Hit.ImpactPoint : End,
		bHit ? FColor::Green : FColor::Red, false, 1.0f);
#endif

	if (bHit && Hit.GetActor() != nullptr)
	{
		UGameplayStatics::ApplyDamage(Hit.GetActor(), 20.0f, GetController(), this, UDamageType::StaticClass());
	}
}
```

**코드 해석**

- `LineTraceSingleByChannel(결과, 시작, 끝, 채널, 옵션)`은 시작에서 끝까지 선을 그어 처음 닿은 것 하나를 찾습니다.
- 끝점은 총구에서 조준 방향으로 `10000`만큼 떨어진 곳입니다. 이 값이 사거리입니다.
- `Params.AddIgnoredActor(this)`로 자기 자신을 검사에서 뺍니다.
- 채널은 Chapter 21에서 만든 `ECC_Attack`을 씁니다. 이 채널에 블록으로 반응하는 대상만 맞습니다.
- `Hit.ImpactPoint`는 선이 닿은 지점입니다. 맞았으면 그 지점까지 초록 선을, 아니면 끝까지 빨간 선을 1초 동안 그립니다.
- 데미지는 Chapter 22의 `ApplyDamage`로 줍니다. 맞는 쪽의 `TakeDamage`가 호출됩니다.

플레이해서 레벨의 캐릭터를 조준하고 오른쪽 클릭하면 선이 그려지고 체력이 깎입니다. 벽이나 바닥은 `ECC_Attack`을 무시하므로 선이 통과합니다. 벽에 막히게 하려면 벽의 콜리전에서 `Attack` 반응을 블록으로 바꿉니다.

### 27-5 발사체 액터 만들기

같은 총을 발사체 방식으로도 만들어 봅니다. 발사체는 스스로 날아가다 부딪히면 데미지를 주고 사라지는 액터입니다.

**툴 → 새 C++ 클래스**로 부모가 액터인 `MyProjectile`을 만듭니다.

```cpp
// MyProjectile.h 의 클래스 안
public:
	AMyProjectile();

protected:
	virtual void BeginPlay() override;

private:
	UFUNCTION()
	void OnHit(UPrimitiveComponent* HitComponent, AActor* OtherActor,
		UPrimitiveComponent* OtherComp, FVector NormalImpulse, const FHitResult& Hit);

	UPROPERTY(VisibleAnywhere)
	class USphereComponent* Collision;

	UPROPERTY(VisibleAnywhere)
	UStaticMeshComponent* Mesh;

	UPROPERTY(VisibleAnywhere)
	class UProjectileMovementComponent* Movement;

	UPROPERTY(EditAnywhere, Category = Attack)
	float Damage = 20.0f;
```

```cpp
// MyProjectile.cpp
#include "MyProjectile.h"
#include "Components/SphereComponent.h"
#include "GameFramework/ProjectileMovementComponent.h"
#include "Kismet/GameplayStatics.h"

AMyProjectile::AMyProjectile()
{
	PrimaryActorTick.bCanEverTick = false;

	Collision = CreateDefaultSubobject<USphereComponent>(TEXT("Collision"));
	Collision->InitSphereRadius(10.0f);
	Collision->SetCollisionProfileName(TEXT("BlockAllDynamic"));
	RootComponent = Collision;

	Mesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"));
	Mesh->SetupAttachment(Collision);
	Mesh->SetCollisionEnabled(ECollisionEnabled::NoCollision);

	Movement = CreateDefaultSubobject<UProjectileMovementComponent>(TEXT("Movement"));
	Movement->InitialSpeed = 3000.0f;
	Movement->MaxSpeed = 3000.0f;
	Movement->ProjectileGravityScale = 0.0f;

	InitialLifeSpan = 3.0f;
}

void AMyProjectile::BeginPlay()
{
	Super::BeginPlay();

	Collision->OnComponentHit.AddDynamic(this, &AMyProjectile::OnHit);

	if (GetInstigator() != nullptr)
	{
		Collision->IgnoreActorWhenMoving(GetInstigator(), true);
	}
}

void AMyProjectile::OnHit(UPrimitiveComponent* HitComponent, AActor* OtherActor,
	UPrimitiveComponent* OtherComp, FVector NormalImpulse, const FHitResult& Hit)
{
	if (OtherActor != nullptr && OtherActor != this)
	{
		UGameplayStatics::ApplyDamage(OtherActor, Damage, GetInstigatorController(), this, UDamageType::StaticClass());
	}

	Destroy();
}
```

**코드 해석**

- `USphereComponent`는 구체 모양의 콜리전입니다. 부딪힘 판정을 맡으므로 루트로 둡니다. 메시는 보이기만 하면 되므로 콜리전을 끕니다.
- `BlockAllDynamic`은 엔진이 제공하는 프리셋으로, 움직이는 물체이면서 모든 것에 막힙니다.
- **프로젝타일 무브먼트**는 발사체 전용 이동 컴포넌트입니다. `InitialSpeed`는 처음 속도, `ProjectileGravityScale`은 중력의 영향입니다. `0`이면 직선으로 날아가고 `1`이면 포물선을 그립니다.
- `InitialLifeSpan`은 액터의 수명입니다. 아무것도 맞히지 못해도 3초 뒤 스스로 사라집니다.
- `OnComponentHit`는 블록 반응으로 부딪혔을 때 방송되는 엔진의 다이내믹 델리게이트입니다. Chapter 21의 반응 표에서 블록이 Hit 이벤트를 일으킨다고 한 것이 이것입니다.
- **인스티게이터**는 이 액터가 생기게 만든 폰, 여기서는 총을 쏜 캐릭터입니다. `IgnoreActorWhenMoving`으로 쏜 사람 자신과는 부딪히지 않게 합니다.
- `GetInstigatorController()`는 쏜 캐릭터의 컨트롤러입니다. 처치 점수를 누구에게 줄지 판단하는 데 쓰입니다.

빌드한 뒤 `MyProjectile`을 부모로 블루프린트 `BP_MyProjectile`을 만들고, `Mesh`에 구체 모형을 지정해 크기를 `0.2`로 줄입니다.

![BP_MyProjectile 블루프린트의 컴포넌트 계층: Collision 아래 Mesh, 그리고 Movement 컴포넌트](./img/27-projectile-components.png)

### 27-6 발사체 스폰

캐릭터가 어떤 발사체를 쏠지 고를 수 있게 변수를 추가합니다.

```cpp
// MyCharacter.h
UPROPERTY(EditAnywhere, Category = Attack)
TSubclassOf<class AMyProjectile> ProjectileClass;
```

`Fire` 함수의 내용을 발사체 스폰으로 바꿉니다.

```cpp
// MyCharacter.cpp
#include "MyProjectile.h"

void AMyCharacter::Fire()
{
	if (ProjectileClass == nullptr)
	{
		return;
	}

	FActorSpawnParameters SpawnParams;
	SpawnParams.Owner = this;
	SpawnParams.Instigator = this;

	GetWorld()->SpawnActor<AMyProjectile>(ProjectileClass, GetMuzzleTransform(), SpawnParams);
}
```

**코드 해석**

- `SpawnActor<타입>(클래스, 트랜스폼, 옵션)`은 Chapter 26의 `Spawn Actor from Class` 노드와 같은 일을 합니다.
- `FActorSpawnParameters`의 `Instigator`에 자신을 넣어야 발사체가 쏜 사람을 알 수 있습니다.
- 프로젝타일 무브먼트는 액터가 바라보는 방향으로 날아가므로, 총구 트랜스폼의 회전이 곧 발사 방향입니다.

빌드하고 캐릭터 블루프린트의 **Projectile Class**에 `BP_MyProjectile`을 지정합니다. 오른쪽 클릭하면 구체가 날아가 적에게 닿으면 데미지를 주고 사라집니다.

### 27-7 따라하기 — 두 가지 총

1. 캐릭터를 1인칭 시점으로 바꾼다.
2. `IA_Fire`를 만들어 `Fire` 함수에 연결한다.
3. 히트스캔으로 구현해 디버그 선과 데미지를 확인한다.
4. `MyProjectile`을 만들고 `Fire`를 발사체 스폰으로 바꿔 확인한다.
5. `ProjectileGravityScale`을 `1`로 바꿔 포물선으로 날아가게 해 본다.
6. 히트스캔과 발사체를 둘 다 남기고 싶다면 `Fire`를 `FireHitscan`과 `FireProjectile` 두 함수로 나누고, 참·거짓 변수 하나로 어느 쪽을 쓸지 고르게 만든다.

### 연습문제

1. 문제: 히트스캔이 어울리는 무기와 발사체가 어울리는 무기를 하나씩 들고 이유를 한 문장씩 쓰세요.
2. 문제: 라인 트레이스에서 `Params.AddIgnoredActor(this)`를 빼면 생길 수 있는 문제를 한 문장으로 쓰세요.
3. 문제: 발사체가 아무것도 맞히지 못했을 때 레벨에 계속 남지 않게 하는 변수 이름을 쓰세요.

### 정답 포인트

- 소총은 즉시 판정이라 히트스캔, 로켓은 날아가는 궤적이 보여야 해서 발사체. 선이 자기 캡슐에 먼저 닿아 자신을 맞힌다. `InitialLifeSpan`.

---

[상위 문서로 돌아가기](./README.md)
