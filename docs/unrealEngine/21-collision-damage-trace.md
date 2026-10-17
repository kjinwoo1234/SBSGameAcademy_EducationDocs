# Chapter 21 충돌 설정과 공격 판정

## 학습 목표

- 콜리전 채널과 세 가지 반응의 의미를 설명할 수 있다.
- 오브젝트 채널과 트레이스 채널을 구분하고 프로젝트에 새 채널과 프리셋을 추가할 수 있다.
- `SweepSingleByChannel`로 공격 범위 안의 액터를 감지할 수 있다.
- 디버그 드로잉으로 판정 범위를 화면에 그려 확인할 수 있다.

## 본문

### 21-1 콜리전

게임 세상에는 가상의 물리 법칙이 있습니다. 캐릭터는 벽을 뚫지 못하고, 던진 물체는 바닥에 떨어집니다. 이런 일을 계산하는 엔진의 부분을 **물리 엔진**이라고 합니다.

물리 엔진은 모형의 겉모습이 아니라 **콜리전**이라는 충돌 영역을 보고 부딪힘을 계산합니다. 콜리전을 만드는 방법은 세 가지입니다.

| 방법 | 내용 | 쓰는 곳 |
|---|---|---|
| 스태틱 메시 에셋에 넣기 | 모형 에셋 안에 단순한 충돌 모양을 심어 둔다 | 벽, 바닥, 소품 |
| 기본 도형 컴포넌트 | 구체, 박스, 캡슐 컴포넌트를 액터에 붙인다 | 캐릭터의 캡슐, 감지 영역 |
| 피직스 에셋 | 캐릭터의 부위마다 도형을 붙여 연결한다 | 쓰러질 때 몸이 축 늘어지는 표현 |

Chapter 12의 박스 콜리전은 두 번째 방법이었습니다.

### 21-2 콜리전 채널과 반응

콜리전이 있다고 해서 모든 것이 서로 막히지는 않습니다. 플레이어는 벽에 막혀야 하지만 감지 영역은 통과해야 합니다. 이것을 정하기 위해 콜리전마다 두 가지를 설정합니다.

첫째는 **콜리전 채널**로, "나는 어떤 종류의 물체인가"를 나타냅니다. 엔진이 기본으로 제공하는 채널은 다음과 같습니다.

| 채널 | 쓰는 대상 |
|---|---|
| WorldStatic | 움직이지 않는 배경. 레벨에 놓인 스태틱 메시 |
| WorldDynamic | 움직이는 물체. 블루프린트 안의 스태틱 메시 |
| Pawn | 플레이어가 조종하는 물체. 캐릭터의 캡슐 |
| PhysicsBody | 물리 시뮬레이션으로 움직이는 물체 |
| Vehicle | 탈것 |
| Destructible | 부서지는 물체 |
| Visibility | 눈에 보이는지 검사할 때 쓰는 채널 |
| Camera | 카메라와 대상 사이에 장애물이 있는지 검사할 때 쓰는 채널 |

둘째는 **콜리전 반응**으로, "상대 채널에 대해 어떻게 행동할 것인가"입니다.

| 반응 | 동작 | 발생하는 이벤트 |
|---|---|---|
| 무시 | 없는 것처럼 통과한다 | 없음 |
| 오버랩 | 통과하지만 겹쳤다는 것을 알린다 | BeginOverlap, EndOverlap |
| 블록 | 통과하지 못하게 막는다 | Hit |

두 물체가 만났을 때 실제 결과는 양쪽의 반응 중 약한 쪽을 따릅니다. 한쪽이 블록이어도 다른 쪽이 무시이면 통과합니다. 오버랩 이벤트를 받으려면 두 컴포넌트 모두 **오버랩 이벤트 생성** 옵션이 켜져 있어야 합니다.

컴포넌트의 디테일 패널에서 **콜리전 → 콜리전 프리셋**을 `Custom`으로 바꾸면 채널마다 반응을 직접 고를 수 있습니다.

![컴포넌트 디테일 패널의 콜리전 프리셋을 Custom으로 펼쳐 오브젝트 타입과 채널별 무시·오버랩·블록 체크박스가 보이는 화면](./img/21-collision-preset-custom.png)

### 21-3 오브젝트 채널과 트레이스 채널

위 표에서 Visibility와 Camera는 물체의 종류가 아닙니다. 채널은 용도에 따라 두 가지로 나뉩니다.

| 종류 | 질문 | 용도 |
|---|---|---|
| 오브젝트 채널 | 나는 누구인가 | 콜리전에 지정해 물체의 종류를 정한다 |
| 트레이스 채널 | 나는 누구를 감지할 것인가 | 코드에서 "이 선에 닿는 것이 있는가"를 검사할 때 쓴다 |

**트레이스**는 보이지 않는 선이나 도형을 쏘아 무엇에 닿는지 알아보는 검사입니다. 총을 쏠 때 조준점 방향으로 선을 그어 적이 있는지 확인하는 것이 대표적입니다. 선 대신 구체나 박스를 한 방향으로 쓸고 지나가며 검사할 수도 있고, 이것을 **스윕**이라고 합니다.

17-7에서 본 스프링 암이 Camera 트레이스 채널을 씁니다. 캐릭터에서 카메라 방향으로 트레이스를 해서 벽에 닿으면 카메라를 그 앞으로 당깁니다.

### 21-4 채널과 프리셋 추가

공격 판정 전용 채널을 만듭니다. **편집 → 프로젝트 세팅 → 엔진 → 콜리전**을 엽니다.

1. **오브젝트 채널**에서 **새 오브젝트 채널**을 누른다. 이름 `MyCharacter`, 기본 반응 `Block`.
2. **트레이스 채널**에서 **새 트레이스 채널**을 누른다. 이름 `Attack`, 기본 반응 `Ignore`.

`Attack`의 기본 반응이 무시이므로, 따로 설정하지 않은 물체는 공격 판정에 걸리지 않습니다. 맞아야 하는 대상만 블록으로 바꿔 주면 됩니다.

채널과 반응 설정을 묶어 이름 붙인 것을 **프리셋**이라고 합니다. 캐릭터용 프리셋을 만듭니다.

3. 같은 창의 **프리셋**을 펼치고 **새로 만들기**를 누른다.
4. 이름 `MyCharacter`, 콜리전 켜짐 `Collision Enabled`, 오브젝트 타입 `MyCharacter`로 정한다.
5. 반응 목록에서 `Visibility`는 무시, `Attack`을 포함한 나머지는 블록으로 정한다.

![프로젝트 세팅 콜리전 창에서 MyCharacter 오브젝트 채널·Attack 트레이스 채널·MyCharacter 프리셋을 추가한 화면](./img/21-project-settings-collision.png)

만든 프리셋을 캐릭터의 캡슐에 지정합니다. 캐릭터 생성자에 한 줄을 추가합니다.

```cpp
GetCapsuleComponent()->SetCollisionProfileName(TEXT("MyCharacter"));
```

블루프린트에서 캡슐 컴포넌트의 콜리전 프리셋을 `MyCharacter`로 고르는 것과 같은 일입니다.

### 21-5 C++에서 채널 이름 찾기

새로 만든 채널을 C++에서 쓰려면 엔진 내부 이름을 알아야 합니다. 엔진은 채널을 모두 32개까지 다루고, 그중 프로젝트가 추가할 수 있는 것은 18개입니다. 추가한 채널에는 `ECC_GameTraceChannel1`부터 `ECC_GameTraceChannel18`까지의 이름이 만든 순서대로 배정됩니다.

어떤 이름이 배정됐는지는 프로젝트 폴더의 `Config/DefaultEngine.ini`에서 확인합니다.

```ini
+DefaultChannelResponses=(Channel=ECC_GameTraceChannel1,DefaultResponse=ECR_Block,bTraceType=False,bStaticObject=False,Name="MyCharacter")
+DefaultChannelResponses=(Channel=ECC_GameTraceChannel2,DefaultResponse=ECR_Ignore,bTraceType=True,bStaticObject=False,Name="Attack")
```

`Attack` 채널은 `ECC_GameTraceChannel2`입니다. 숫자가 그대로 코드에 흩어져 있으면 읽기 어려우므로 `Practice.h`에 이름을 붙여 둡니다.

```cpp
// Practice.h
#define ECC_Attack ECC_GameTraceChannel2
```

채널을 만든 순서가 다르면 번호가 다를 수 있습니다. 반드시 자신의 `DefaultEngine.ini`를 확인하고 적으세요.

### 21-6 공격 판정 함수

Chapter 20에서 `AttackHitCheck` 노티파이가 `OnAttackHitCheck` 델리게이트를 방송하게 만들었습니다. 이 방송을 캐릭터가 받아 판정합니다. 판정 방법은 반지름 50cm의 구체를 캐릭터 위치에서 앞으로 2m 쓸어 보는 것입니다.

캐릭터 헤더에 추가합니다.

```cpp
// MyCharacter.h
private:
	void AttackCheck();

	UPROPERTY(EditAnywhere, Category = Attack)
	float AttackRange = 200.0f;

	UPROPERTY(EditAnywhere, Category = Attack)
	float AttackRadius = 50.0f;
```

`PostInitializeComponents`의 다른 등록 아래에 한 줄을 추가합니다.

```cpp
MyAnim->OnAttackHitCheck.AddUObject(this, &AMyCharacter::AttackCheck);
```

판정 함수를 만듭니다.

```cpp
// MyCharacter.cpp
#include "Practice.h"

void AMyCharacter::AttackCheck()
{
	FHitResult HitResult;
	FCollisionQueryParams Params(NAME_None, false, this);

	const FVector Start = GetActorLocation();
	const FVector End = Start + GetActorForwardVector() * AttackRange;

	const bool bResult = GetWorld()->SweepSingleByChannel(
		HitResult,
		Start,
		End,
		FQuat::Identity,
		ECC_Attack,
		FCollisionShape::MakeSphere(AttackRadius),
		Params);

	if (bResult && HitResult.GetActor() != nullptr)
	{
		PLOG(Warning, TEXT("Hit Actor : %s"), *HitResult.GetActor()->GetName());
	}
}
```

**코드 해석**

`SweepSingleByChannel`은 도형을 시작점에서 끝점까지 쓸어 처음 닿은 것 하나를 찾는 함수입니다. 닿은 것이 있으면 참을 돌려줍니다.

| 매개변수 | 넣은 값 | 의미 |
|---|---|---|
| `HitResult` | `HitResult` | 닿은 대상의 정보를 담아 올 구조체 |
| `Start` | 캐릭터 위치 | 쓸기 시작하는 위치 |
| `End` | 앞으로 `AttackRange`만큼 | 쓸기 끝나는 위치 |
| `Rot` | `FQuat::Identity` | 도형의 회전. 회전 없음 |
| `TraceChannel` | `ECC_Attack` | 검사에 쓸 트레이스 채널 |
| `CollisionShape` | 반지름 `AttackRadius`인 구체 | 쓸 도형 |
| `Params` | `Params` | 검사 옵션 |

- `FCollisionQueryParams`의 세 번째 값 `this`는 검사에서 제외할 액터입니다. 넣지 않으면 자기 자신의 캡슐이 가장 먼저 닿아 버립니다.
- `GetActorForwardVector()`는 액터가 바라보는 방향을 가리키는 길이 1의 벡터입니다. 여기에 거리를 곱해 시작점에 더하면 앞쪽의 한 지점이 됩니다.
- `HitResult.GetActor()`는 닿은 액터를 돌려줍니다.
- `AddUObject`는 Chapter 20의 표에서 본 대로 일반 델리게이트에 멤버 함수를 등록합니다.

테스트하려면 맞을 상대가 필요합니다. 콘텐츠 브라우저에서 `BP_ThirdPersonCharacter`를 레벨에 하나 끌어다 놓습니다. 플레이해서 그 앞에서 공격하면 `Hit Actor` 로그가 찍히고, 허공이나 벽을 치면 찍히지 않습니다. 벽은 `Attack` 채널에 대한 기본 반응이 무시이기 때문입니다.

### 21-7 디버그 드로잉

판정 범위가 눈에 보이지 않아서, 맞았는지 확인하려면 매번 로그 창을 봐야 합니다. 엔진은 개발 중에 선이나 도형을 화면에 임시로 그려 주는 **디버그 드로잉** 기능을 제공합니다. 구체가 쓸고 지나간 영역은 캡슐 모양이므로 캡슐을 그립니다.

`MyCharacter.cpp` 위쪽에 헤더를 포함합니다.

```cpp
#include "DrawDebugHelpers.h"
```

`AttackCheck` 함수의 `SweepSingleByChannel` 호출 바로 아래에 추가합니다.

```cpp
#if ENABLE_DRAW_DEBUG
	const FVector TraceVec = GetActorForwardVector() * AttackRange;
	const FVector Center = Start + TraceVec * 0.5f;
	const float HalfHeight = AttackRange * 0.5f + AttackRadius;
	const FQuat CapsuleRot = FRotationMatrix::MakeFromZ(TraceVec).ToQuat();
	const FColor DrawColor = bResult ? FColor::Green : FColor::Red;
	const float DebugLifeTime = 2.0f;

	DrawDebugCapsule(GetWorld(), Center, HalfHeight, AttackRadius, CapsuleRot, DrawColor, false, DebugLifeTime);
#endif
```

**코드 해석**

- `#if ENABLE_DRAW_DEBUG`와 `#endif` 사이의 코드는 개발용 빌드에서만 포함됩니다. 배포할 때는 자동으로 빠집니다.
- `Center`는 캡슐의 중심으로, 시작점과 끝점의 가운데입니다.
- `HalfHeight`는 캡슐 길이의 절반입니다. 쓴 거리의 절반에 구체 반지름을 더합니다.
- 캡슐은 기본적으로 세로로 서 있습니다. `FRotationMatrix::MakeFromZ(TraceVec)`는 캡슐의 세로 방향이 공격 방향을 향하도록 눕히는 회전을 만듭니다.
- `조건 ? A : B`는 조건이 참이면 A, 거짓이면 B를 고릅니다. 맞았으면 초록, 아니면 빨강으로 그립니다.
- `DrawDebugCapsule`의 마지막 두 값은 계속 남길지와 화면에 남아 있을 시간입니다.

![공격 시 캐릭터 앞에 그려진 디버그 캡슐: 맞았을 때 초록색, 빗나갔을 때 빨간색](./img/21-debug-capsule.png)

플레이해서 공격하면 판정 범위가 2초 동안 보입니다. `AttackRange`와 `AttackRadius`를 디테일 패널에서 바꿔 가며 범위를 조절합니다.

### 21-8 따라하기 — 공격 판정

1. 프로젝트 세팅에 `MyCharacter` 오브젝트 채널, `Attack` 트레이스 채널, `MyCharacter` 프리셋을 추가한다.
2. 캐릭터 캡슐에 프리셋을 지정한다.
3. `DefaultEngine.ini`에서 `Attack` 채널의 번호를 확인하고 `ECC_Attack`을 정의한다.
4. `AttackCheck`를 만들어 `OnAttackHitCheck`에 등록한다.
5. 레벨에 캐릭터를 하나 더 놓고 공격해 로그를 확인한다.
6. 디버그 캡슐을 추가해 맞았을 때와 빗나갔을 때의 색을 확인한다.
7. 프리셋에서 `Attack` 반응을 무시로 바꿔 보고, 같은 자리에서 공격했을 때 결과가 어떻게 달라지는지 확인한 뒤 되돌린다.

맞은 액터의 체력을 깎는 방법은 Chapter 22에서 다룹니다.

### 연습문제

1. 문제: 한쪽 물체의 반응이 블록이고 다른 쪽이 오버랩일 때 두 물체가 만나면 어떻게 되는지 쓰세요.  
   - 조건: 21-2의 규칙을 근거로 쓸 것
2. 문제: `Attack` 트레이스 채널의 기본 반응을 무시로 만든 이유를 한 문장으로 쓰세요.
3. 문제: `AttackCheck`에서 `FCollisionQueryParams`에 `this`를 넣지 않으면 생기는 문제를 한 문장으로 쓰세요.

### 정답 포인트

- 약한 쪽을 따라 오버랩이 되어 통과하면서 이벤트가 발생한다. 맞아야 하는 대상만 블록으로 지정하면 나머지는 판정에서 빠지게 하려고. 자기 자신의 캡슐이 먼저 닿아 자신을 때린 것으로 판정된다.

---

[상위 문서로 돌아가기](./README.md)
