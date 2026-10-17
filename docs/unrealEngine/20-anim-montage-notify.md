# Chapter 20 애니메이션 몽타주·델리게이트·노티파이

## 학습 목표

- 애니메이션 몽타주를 만들고 C++에서 재생할 수 있다.
- 델리게이트에 함수를 등록해 몽타주가 끝나는 시점을 전달받을 수 있다.
- 애니메이션 노티파이로 동작 중 특정 순간에 C++ 함수를 호출할 수 있다.
- 몽타주 섹션과 노티파이를 이용해 콤보 공격을 구현할 수 있다.

## 본문

### 20-1 애니메이션 몽타주

마우스를 클릭할 때마다 캐릭터가 검을 휘두르게 만들려고 합니다. 공격 동작은 네 개이고, 연달아 클릭하면 네 동작이 순서대로 이어지는 콤보 공격이 되어야 합니다. 클릭이 늦으면 콤보가 끊기고 처음부터 다시 시작합니다.

Chapter 18의 스테이트 머신으로도 만들 수 있습니다. 공격 스테이트 네 개를 추가하고 서로 화살표로 이으면 됩니다. 하지만 동작이 추가될 때마다 스테이트와 화살표가 늘어나 스테이트 머신이 금방 복잡해집니다.

**애니메이션 몽타주**는 스테이트 머신과 따로, 코드에서 "지금 이 동작을 재생해"라고 명령해서 끼워 넣는 애니메이션 에셋입니다. 몽타주는 영화에서 여러 장면을 잘라 붙여 새 장면을 만드는 기법의 이름입니다. 애니메이션 몽타주도 여러 시퀀스를 잘라 붙여 하나로 묶습니다.

### 20-2 몽타주 에셋 만들기

1. 콘텐츠 브라우저를 우클릭해 **애니메이션 → 애니메이션 몽타주**를 누른다.
2. 스켈레톤으로 `SK_Mannequin`을 고르고 이름을 `AM_Attack`으로 짓는다.
3. 더블클릭해 몽타주 에디터를 연다.
4. 타임라인 위쪽 **몽타주** 트랙에 `Default`라는 **섹션**이 하나 있다. 우클릭해 이름을 `Attack1`로 바꾼다.
5. 애셋 브라우저에서 Chapter 19에서 익스포트한 공격 시퀀스 네 개를 순서대로 타임라인으로 끌어다 놓는다.

![몽타주 에디터: 위쪽 몽타주 트랙의 섹션 이름, 그 아래 지그재그로 배치된 공격 시퀀스 네 개, 아래쪽 노티파이 트랙](./img/20-montage-editor.png)

**섹션**은 몽타주 안에서 이름을 붙여 구분한 구간입니다. 지금은 `Attack1` 섹션 하나에 동작 네 개가 모두 들어 있어서, 재생 버튼을 누르면 네 동작이 처음부터 끝까지 이어서 재생됩니다.

### 20-3 공격 입력 만들기

Chapter 17에서 배운 방법으로 공격 입력을 추가합니다.

1. `콘텐츠/ThirdPerson/Input/Actions`에 입력 액션 `IA_Attack`을 만든다. 값 타입은 `Digital (bool)`이다.
2. `IMC_Default`를 열어 매핑을 추가하고 `IA_Attack`에 **왼쪽 마우스 버튼**을 연결한다.
3. 캐릭터 헤더에 액션 변수와 함수를 추가한다.

```cpp
// MyCharacter.h
UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, meta = (AllowPrivateAccess = "true"))
UInputAction* AttackAction;

void Attack();
```

4. `SetupPlayerInputComponent`의 다른 `BindAction` 아래에 한 줄을 추가하고 함수를 만든다.

```cpp
// MyCharacter.cpp
EnhancedInputComponent->BindAction(AttackAction, ETriggerEvent::Started, this, &AMyCharacter::Attack);
```

```cpp
void AMyCharacter::Attack()
{
	PLOG_S(Warning);
}
```

5. 빌드하고 `BP_ThirdPersonCharacter`의 디테일 패널에서 **Attack Action**에 `IA_Attack`을 지정한다.
6. 플레이해서 클릭할 때마다 로그가 찍히는지 확인한다.

### 20-4 몽타주 재생 함수

몽타주 재생은 애님 인스턴스가 담당합니다. Chapter 18에서 만든 `MyAnimInstance`에 추가합니다.

```cpp
// MyAnimInstance.h 의 클래스 안
public:
	void PlayAttackMontage();

private:
	UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category = Attack, meta = (AllowPrivateAccess = "true"))
	UAnimMontage* AttackMontage;
```

```cpp
// MyAnimInstance.cpp
void UMyAnimInstance::PlayAttackMontage()
{
	if (AttackMontage == nullptr)
	{
		return;
	}

	if (Montage_IsPlaying(AttackMontage))
	{
		return;
	}

	Montage_Play(AttackMontage, 1.0f);
}
```

**코드 해석**

- 첫 번째 `if`는 몽타주가 지정되지 않았을 때 재생을 시도하지 않게 막습니다.
- `Montage_IsPlaying(몽타주)`는 그 몽타주가 재생 중이면 참을 돌려줍니다. 재생 중에 또 재생하면 동작이 처음으로 튀므로 막습니다.
- `Montage_Play(몽타주, 재생 속도)`가 실제 재생 명령입니다.

빌드한 뒤 `ABP_MyAnim`을 열고 **클래스 디폴트**를 눌러 **Attack Montage**에 `AM_Attack`을 지정합니다.

### 20-5 슬롯 노드

재생 명령을 내려도 애님 그래프가 몽타주를 받아 줄 자리가 없으면 화면에 나타나지 않습니다. 몽타주가 끼어들 자리를 **슬롯**이라고 합니다.

1. `ABP_MyAnim`의 애님 그래프에서 스테이트 머신과 `Output Pose` 사이의 선을 끊는다.
2. 우클릭해 `slot`을 검색하고 **Slot 'DefaultSlot'** 노드를 만든다.
3. 스테이트 머신 → 슬롯 → `Output Pose` 순서로 연결하고 컴파일한다.

![애님 그래프: 스테이트 머신 → Slot 'DefaultSlot' → Output Pose 순서로 연결된 노드](./img/20-slot-node.png)

슬롯 노드는 평소에는 왼쪽에서 들어온 동작을 그대로 통과시키고, 몽타주가 재생되는 동안에는 몽타주 동작으로 바꿔 내보냅니다. 스테이트 머신 뒤에 두었으므로 서 있든 달리든 어떤 상황에서도 공격 동작이 나옵니다.

이제 캐릭터의 `Attack`에서 재생 함수를 호출합니다.

```cpp
// MyCharacter.cpp
#include "MyAnimInstance.h"

void AMyCharacter::Attack()
{
	UMyAnimInstance* AnimInstance = Cast<UMyAnimInstance>(GetMesh()->GetAnimInstance());
	if (AnimInstance != nullptr)
	{
		AnimInstance->PlayAttackMontage();
	}
}
```

`GetMesh()`는 캐릭터의 스켈레탈 메시 컴포넌트를, `GetAnimInstance()`는 그 메시가 쓰는 애님 인스턴스를 가져옵니다. 빌드하고 플레이해서 클릭하면 공격 동작 네 개가 이어서 재생됩니다.

### 20-6 델리게이트

지금은 애님 인스턴스가 "재생 중이면 무시"를 판단합니다. 그런데 공격 중인지는 캐릭터도 알아야 합니다. 공격 중에는 이동을 막거나 다른 행동을 못 하게 해야 하기 때문입니다. 캐릭터가 매 프레임 "아직 재생 중이야?"라고 물어볼 수도 있지만, 몽타주가 끝났을 때 애님 인스턴스가 캐릭터에게 알려 주는 편이 낫습니다.

**델리게이트**는 "이 일이 일어나면 불러 줄 함수"를 등록해 두는 목록입니다. 일이 일어나는 쪽은 목록에 누가 등록했는지 몰라도 되고, 그저 목록에 있는 함수를 모두 호출하면 됩니다.

1. 캐릭터가 애님 인스턴스의 델리게이트에 자기 함수를 등록한다.
2. 몽타주가 끝나면 애님 인스턴스가 델리게이트를 실행한다.
3. 등록되어 있던 캐릭터의 함수가 호출된다.

애님 인스턴스에는 몽타주가 끝나면 실행되는 `OnMontageEnded` 델리게이트가 이미 있습니다. 여기에 등록할 수 있는 함수는 `UAnimMontage*`와 `bool`을 매개변수로 받는 함수입니다.

캐릭터 헤더에 추가합니다.

```cpp
// MyCharacter.h
public:
	virtual void PostInitializeComponents() override;

private:
	UFUNCTION()
	void OnAttackMontageEnded(UAnimMontage* Montage, bool bInterrupted);

	UPROPERTY(VisibleInstanceOnly, BlueprintReadOnly, Category = Attack, meta = (AllowPrivateAccess = "true"))
	bool bIsAttacking = false;

	UPROPERTY()
	class UMyAnimInstance* MyAnim;
```

```cpp
// MyCharacter.cpp
void AMyCharacter::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	MyAnim = Cast<UMyAnimInstance>(GetMesh()->GetAnimInstance());
	if (MyAnim != nullptr)
	{
		MyAnim->OnMontageEnded.AddDynamic(this, &AMyCharacter::OnAttackMontageEnded);
	}
}

void AMyCharacter::Attack()
{
	if (bIsAttacking || MyAnim == nullptr)
	{
		return;
	}

	MyAnim->PlayAttackMontage();
	bIsAttacking = true;
}

void AMyCharacter::OnAttackMontageEnded(UAnimMontage* Montage, bool bInterrupted)
{
	bIsAttacking = false;
}
```

**코드 해석**

- `PostInitializeComponents`는 Chapter 15의 표에서 본 대로 컴포넌트 준비가 끝난 뒤 호출됩니다. 애님 인스턴스를 변수에 담아 두고 델리게이트에 등록하기 좋은 시점입니다.
- `AddDynamic(대상 객체, 함수 주소)`가 등록입니다. 이 방식으로 등록하는 함수에는 반드시 `UFUNCTION()`을 붙여야 합니다.
- `bInterrupted`는 몽타주가 끝까지 재생되지 않고 중간에 끊겼을 때 참입니다.
- `VisibleInstanceOnly`는 레벨에 놓인 인스턴스의 디테일 패널에서만 보이게 하는 지정자입니다. 플레이 중 값이 바뀌는 것을 확인할 때 씁니다.
- `class UMyAnimInstance*`의 `class`는 헤더를 포함하지 않고 "이런 클래스가 있다"고만 알리는 표기입니다. 헤더끼리 서로 포함하는 일을 줄여 줍니다.

### 20-7 애니메이션 노티파이

검을 휘두르는 동작에서 실제로 적이 맞는 순간은 검이 앞을 지나가는 때입니다. 클릭한 순간이 아닙니다. 동작의 특정 시점에 코드를 실행하려면 그 시점을 표시해 둘 방법이 필요합니다.

**애니메이션 노티파이**는 애니메이션 타임라인 위에 꽂아 두는 표시입니다. 재생이 그 지점을 지나가면 애님 인스턴스에 신호가 갑니다.

1. `AM_Attack`을 열고 아래쪽 **노티파이** 트랙에서 첫 번째 공격의 검이 앞을 지나는 지점을 우클릭한다.
2. **노티파이 추가 → 새 노티파이**를 누르고 이름을 `AttackHitCheck`로 짓는다.
3. 나머지 세 공격에도 같은 이름의 노티파이를 꽂는다. 두 번째부터는 목록에서 `AttackHitCheck`를 고르면 된다.

엔진은 노티파이를 지날 때 애님 인스턴스에서 `AnimNotify_노티파이이름`이라는 함수를 찾아 호출합니다. 이름으로 찾기 때문에 반드시 `UFUNCTION()`이 붙어 있어야 합니다.

```cpp
// MyAnimInstance.h 의 클래스 안
private:
	UFUNCTION()
	void AnimNotify_AttackHitCheck();
```

```cpp
// MyAnimInstance.cpp
#include "Practice.h"

void UMyAnimInstance::AnimNotify_AttackHitCheck()
{
	PLOG_S(Warning);
}
```

빌드하고 공격하면 동작 하나마다 로그가 한 줄씩, 모두 네 줄 찍힙니다.

### 20-8 섹션 나누기

콤보를 만들려면 공격 하나하나를 따로 재생할 수 있어야 합니다.

1. `AM_Attack`의 몽타주 트랙에서 두 번째 공격이 시작되는 지점을 우클릭해 **새 몽타주 섹션**을 누르고 이름을 `Attack2`로 짓는다.
2. 같은 방법으로 `Attack3`, `Attack4`를 만든다.
3. 에디터 오른쪽 아래 **몽타주 섹션** 탭을 연다. 섹션들이 화살표로 이어져 있다.
4. **링크 지우기**를 눌러 모든 연결을 끊는다.

![몽타주 섹션 탭: Attack1~Attack4 섹션이 서로 연결되지 않고 따로 나열된 화면](./img/20-montage-sections-unlinked.png)

섹션이 이어져 있으면 한 섹션이 끝난 뒤 다음 섹션으로 자동으로 넘어갑니다. 연결을 끊으면 섹션 하나가 끝나는 순간 몽타주가 끝납니다.

다음 공격으로 넘어갈지 판단할 시점도 노티파이로 표시합니다.

5. 노티파이 트랙 이름 옆 메뉴에서 **노티파이 트랙 추가**를 눌러 두 번째 트랙을 만든다.
6. 새 트랙에서 첫 번째 공격의 휘두르기가 끝나갈 무렵을 우클릭해 `NextAttackCheck` 노티파이를 꽂는다.
7. 두 번째, 세 번째 공격에도 꽂는다. 네 번째 공격은 다음 콤보가 없으므로 꽂지 않는다.
8. `NextAttackCheck` 노티파이를 하나씩 선택하고 디테일 패널의 **몽타주 틱 타입**을 `Branching Point`로 바꾼다.

| 몽타주 틱 타입 | 신호를 받는 방식 | 쓰는 곳 |
|---|---|---|
| `Queued` | 조금 늦게 전달될 수 있다 | 소리, 이펙트처럼 약간 늦어도 되는 것 |
| `Branching Point` | 정확히 그 프레임에 전달된다 | 섹션을 바꾸는 것처럼 타이밍이 중요한 것 |

### 20-9 콤보 공격 구현

콤보의 규칙을 정리합니다.

- 공격 중에 클릭하면 "다음 공격 예약"으로 기록해 둔다.
- `NextAttackCheck` 시점에 예약이 있으면 다음 섹션으로 넘어간다.
- 예약이 없으면 섹션이 끝나며 몽타주가 끝나고 콤보가 초기화된다.

애님 인스턴스에 섹션 이동 함수와 직접 만든 델리게이트를 추가합니다.

```cpp
// MyAnimInstance.h — UCLASS() 위
DECLARE_MULTICAST_DELEGATE(FOnNextAttackCheckDelegate);
DECLARE_MULTICAST_DELEGATE(FOnAttackHitCheckDelegate);
```

```cpp
// MyAnimInstance.h 의 클래스 안
public:
	void JumpToAttackMontageSection(int32 NewSection);

	FOnNextAttackCheckDelegate OnNextAttackCheck;
	FOnAttackHitCheckDelegate OnAttackHitCheck;

private:
	UFUNCTION()
	void AnimNotify_NextAttackCheck();

	FName GetAttackMontageSectionName(int32 Section);
```

```cpp
// MyAnimInstance.cpp
void UMyAnimInstance::PlayAttackMontage()
{
	if (AttackMontage != nullptr)
	{
		Montage_Play(AttackMontage, 1.0f);
	}
}

void UMyAnimInstance::JumpToAttackMontageSection(int32 NewSection)
{
	Montage_JumpToSection(GetAttackMontageSectionName(NewSection), AttackMontage);
}

void UMyAnimInstance::AnimNotify_AttackHitCheck()
{
	OnAttackHitCheck.Broadcast();
}

void UMyAnimInstance::AnimNotify_NextAttackCheck()
{
	OnNextAttackCheck.Broadcast();
}

FName UMyAnimInstance::GetAttackMontageSectionName(int32 Section)
{
	return FName(*FString::Printf(TEXT("Attack%d"), Section));
}
```

**코드 해석**

- `DECLARE_MULTICAST_DELEGATE(타입이름)`은 매개변수 없는 델리게이트 타입을 만드는 매크로입니다. 함수를 여러 개 등록할 수 있습니다.
- `Broadcast()`는 델리게이트에 등록된 함수를 모두 호출합니다. 방송국이 여러 텔레비전에 한꺼번에 영상을 내보내는 것과 같습니다.
- `Montage_JumpToSection(섹션 이름, 몽타주)`는 재생 위치를 그 섹션의 시작으로 옮깁니다.
- `GetAttackMontageSectionName`은 숫자 `2`를 받으면 `Attack2`라는 이름을 만들어 돌려줍니다.
- 애님 인스턴스는 노티파이가 왔다는 사실을 방송할 뿐, 누가 듣고 무엇을 하는지는 모릅니다. 캐릭터 클래스를 전혀 몰라도 되므로 두 클래스가 서로 얽히지 않습니다.

캐릭터에 콤보 상태 변수와 함수를 추가합니다.

```cpp
// MyCharacter.h
private:
	void AttackStartComboState();
	void AttackEndComboState();

	bool bCanNextCombo = false;
	bool bIsComboInputOn = false;
	int32 CurrentCombo = 0;
	int32 MaxCombo = 4;
```

```cpp
// MyCharacter.cpp
void AMyCharacter::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	MyAnim = Cast<UMyAnimInstance>(GetMesh()->GetAnimInstance());
	if (MyAnim == nullptr)
	{
		return;
	}

	MyAnim->OnMontageEnded.AddDynamic(this, &AMyCharacter::OnAttackMontageEnded);

	MyAnim->OnNextAttackCheck.AddLambda([this]()
	{
		bCanNextCombo = false;

		if (bIsComboInputOn)
		{
			AttackStartComboState();
			MyAnim->JumpToAttackMontageSection(CurrentCombo);
		}
	});
}

void AMyCharacter::Attack()
{
	if (MyAnim == nullptr)
	{
		return;
	}

	if (bIsAttacking)
	{
		if (bCanNextCombo)
		{
			bIsComboInputOn = true;
		}
	}
	else
	{
		AttackStartComboState();
		MyAnim->PlayAttackMontage();
		MyAnim->JumpToAttackMontageSection(CurrentCombo);
		bIsAttacking = true;
	}
}

void AMyCharacter::OnAttackMontageEnded(UAnimMontage* Montage, bool bInterrupted)
{
	bIsAttacking = false;
	AttackEndComboState();
}

void AMyCharacter::AttackStartComboState()
{
	bCanNextCombo = true;
	bIsComboInputOn = false;
	CurrentCombo = FMath::Clamp(CurrentCombo + 1, 1, MaxCombo);
}

void AMyCharacter::AttackEndComboState()
{
	bIsComboInputOn = false;
	bCanNextCombo = false;
	CurrentCombo = 0;
}
```

**코드 해석**

- `AddLambda`는 이름 없는 함수인 람다를 델리게이트에 등록합니다. `[this]`는 람다 안에서 이 캐릭터의 멤버 변수와 함수를 쓰겠다는 표시입니다.
- `Attack`은 공격 중이 아니면 콤보 1번을 시작하고, 공격 중이면 다음 공격을 예약합니다.
- `bCanNextCombo`는 지금 예약을 받아도 되는지를 나타냅니다. `NextAttackCheck` 시점이 지나면 거짓이 되어 늦은 클릭은 무시됩니다.
- `AttackStartComboState`는 콤보 번호를 하나 올리고 다음 예약을 받을 준비를 합니다. `FMath::Clamp(값, 최소, 최대)`는 값을 범위 안으로 가둡니다.
- `NextAttackCheck` 람다는 예약이 있으면 콤보 번호를 올리고 그 번호의 섹션으로 넘어갑니다. 예약이 없으면 아무것도 하지 않고, 섹션이 끝나면서 `OnAttackMontageEnded`가 호출되어 콤보가 초기화됩니다.

빌드하고 플레이합니다. 한 번 클릭하면 첫 공격만, 타이밍에 맞춰 연달아 클릭하면 네 번째 공격까지 이어집니다.

### 20-10 델리게이트 등록 방법 정리

이 장에서 두 가지 등록 방법을 썼습니다.

| 등록 함수 | 등록하는 것 | 조건 |
|---|---|---|
| `AddDynamic(객체, &클래스::함수)` | 멤버 함수 | 다이내믹 델리게이트 전용. 함수에 `UFUNCTION()` 필요 |
| `AddLambda([this]() { ... })` | 람다 | 일반 델리게이트 전용 |
| `AddUObject(객체, &클래스::함수)` | 멤버 함수 | 일반 델리게이트 전용. `UFUNCTION()` 없어도 된다 |

**다이내믹 델리게이트**는 블루프린트에서도 쓸 수 있도록 리플렉션에 등록되는 델리게이트이고, 엔진의 `OnMontageEnded`가 여기에 해당합니다. 우리가 `DECLARE_MULTICAST_DELEGATE`로 만든 것은 C++에서만 쓰는 일반 델리게이트입니다. `AddUObject`는 Chapter 21에서 씁니다.

### 20-11 따라하기 — 콤보 공격

1. `AM_Attack` 몽타주를 만들고 공격 시퀀스 네 개를 넣는다.
2. `IA_Attack` 입력을 만들어 `Attack` 함수에 연결한다.
3. `PlayAttackMontage`와 슬롯 노드를 추가해 클릭하면 공격이 재생되게 한다.
4. `OnMontageEnded`에 함수를 등록해 `bIsAttacking`을 관리한다.
5. `AttackHitCheck` 노티파이를 꽂고 로그로 호출을 확인한다.
6. 섹션을 네 개로 나누고 링크를 지운 뒤 `NextAttackCheck` 노티파이를 꽂는다.
7. 콤보 코드를 넣고 한 번 클릭과 연타의 차이를 확인한다.
8. 레벨의 캐릭터를 선택해 플레이 중 디테일 패널에서 `bIsAttacking`이 바뀌는 것을 본다.

`AttackHitCheck` 시점에 실제로 적이 맞았는지 판정하는 방법은 Chapter 21에서 다룹니다.

### 연습문제

1. 문제: 몽타주 재생 명령을 내렸는데 캐릭터가 공격 동작을 하지 않을 때 애님 그래프에서 확인할 노드를 쓰세요.
2. 문제: `AnimNotify_AttackHitCheck` 함수에서 `UFUNCTION()`을 빼면 어떻게 되는지 한 문장으로 쓰세요.  
   - 조건: 엔진이 함수를 찾는 방식을 언급할 것
3. 문제: 콤보 3번 공격 중 `NextAttackCheck` 시점이 지난 뒤에 클릭하면 어떻게 되는지 변수 이름을 써서 설명하세요.  
   - 조건: `bCanNextCombo`를 포함할 것

### 정답 포인트

- Slot 'DefaultSlot' 노드. 엔진이 이름으로 함수를 찾는데 리플렉션에 등록되지 않아 호출되지 않는다. `bCanNextCombo`가 거짓이라 예약되지 않고, 섹션이 끝나면 콤보가 초기화된다.

---

[상위 문서로 돌아가기](./README.md)
