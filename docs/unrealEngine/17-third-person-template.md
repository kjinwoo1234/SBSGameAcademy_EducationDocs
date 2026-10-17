# Chapter 17 3인칭 템플릿 분석

## 학습 목표

- 3인칭 템플릿의 게임모드와 캐릭터가 어떻게 연결되어 있는지 설명할 수 있다.
- `Config`, `BlueprintReadOnly`, `meta` 지정자의 쓰임을 구분할 수 있다.
- 캐릭터 생성자의 캡슐·무브먼트·스프링 암·카메라 설정을 읽고 값을 바꿀 수 있다.
- Enhanced Input의 액션과 매핑 컨텍스트가 C++ 함수에 연결되는 흐름을 설명할 수 있다.

## 본문

### 17-1 템플릿을 프로젝트에 추가하기

잘 만들어진 코드를 읽는 것은 좋은 공부 방법입니다. 에픽게임즈가 만든 3인칭 템플릿의 C++ 코드를 한 줄씩 읽으며 캐릭터가 어떻게 만들어지는지 배웁니다.

1. 콘텐츠 브라우저에서 **추가 → 피처 또는 콘텐츠 팩 추가**를 누른다.
2. **C++ 피처** 탭에서 **삼인칭**을 고르고 **프로젝트에 추가**를 누른다.
3. 에디터를 닫고 Visual Studio에서 빌드한 뒤 다시 연다.
4. `콘텐츠/ThirdPerson/Maps`의 `ThirdPersonMap` 레벨을 연다.

`Source` 폴더에 캐릭터 클래스와 게임모드 클래스가 추가됩니다. 추가된 클래스의 이름은 `ATP_ThirdPersonCharacter`, `ATP_ThirdPersonGameMode`처럼 길고, 엔진 버전에 따라 다릅니다. 이 과정에서는 캐릭터 클래스를 줄여서 `AMyCharacter`로 적습니다. 코드를 따라 칠 때는 자신의 프로젝트에 생긴 실제 이름으로 바꿔 적으세요.

최신 엔진 버전의 템플릿은 입력 처리 일부가 다른 클래스로 옮겨져 있어 아래 코드와 구성이 다를 수 있습니다. 함수 이름이 달라도 하는 일은 같으므로, 같은 역할을 하는 코드를 찾아 대조하며 읽습니다.

### 17-2 게임모드가 폰을 지정하는 방법

월드 세팅을 열면 이 레벨의 게임모드가 템플릿의 게임모드로 되어 있고, 디폴트 폰 클래스만 바뀌어 있습니다. 게임모드 코드를 봅니다.

```cpp
ATP_ThirdPersonGameMode::ATP_ThirdPersonGameMode()
{
	static ConstructorHelpers::FClassFinder<APawn> PlayerPawnBPClass(
		TEXT("/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter"));

	if (PlayerPawnBPClass.Class != nullptr)
	{
		DefaultPawnClass = PlayerPawnBPClass.Class;
	}
}
```

**코드 해석**

- `FClassFinder`는 Chapter 13의 `FObjectFinder`와 비슷하지만 에셋이 아니라 **클래스**를 찾습니다. 블루프린트 클래스의 경로를 주면 그 클래스를 가져옵니다.
- 찾은 클래스를 `DefaultPawnClass`에 넣습니다.

게임모드가 하는 일은 이것뿐입니다. 이 템플릿의 모든 기능은 폰 쪽에 들어 있다는 뜻입니다.

콘텐츠 브라우저에서 `BP_ThirdPersonCharacter`를 열어 보면 오른쪽 위에 부모 클래스가 C++ 캐릭터 클래스로 표시됩니다. 이벤트 그래프에는 기능이 없습니다. Chapter 13에서 배운 대로 기능은 C++에 두고, 블루프린트는 모형과 수치를 정하는 용도로만 쓴 구조입니다.

### 17-3 Config 지정자

캐릭터 헤더의 클래스 선언은 이렇게 시작합니다.

```cpp
UCLASS(config=Game)
class AMyCharacter : public ACharacter
```

`config=Game`은 이 클래스의 일부 변수 값을 설정 파일에서 읽어 올 수 있게 한다는 뜻입니다. 코드를 다시 빌드하지 않고 글자 파일만 고쳐 값을 바꿀 수 있습니다.

직접 해 봅니다. 헤더에 변수를 추가합니다.

```cpp
UPROPERTY(Config)
int32 MyCustomProperty;
```

프로젝트 폴더의 `Config/DefaultGame.ini` 파일을 메모장으로 열어 맨 아래에 추가합니다.

```ini
[/Script/Practice.MyCharacter]
MyCustomProperty=3
```

`BeginPlay`에서 값을 로그로 확인합니다. Chapter 16에서 만든 `PLOG`를 쓰기 위해 캐릭터 소스 파일 위쪽에 `#include "Practice.h"`를 추가합니다.

```cpp
PLOG(Warning, TEXT("MyCustomProperty = %d"), MyCustomProperty);
```

```text
PracticeLog: Warning: AMyCharacter::BeginPlay(52) MyCustomProperty = 3
```

**코드 해석**

- `UPROPERTY(Config)`가 붙은 변수는 설정 파일에서 값을 읽습니다.
- 대괄호 안은 클래스의 주소입니다. `/Script/모듈이름.클래스이름` 형태이고 클래스 이름에서 접두사 `A`는 뺍니다. 여기에는 줄인 이름이 아니라 프로젝트에 있는 실제 클래스 이름을 적어야 합니다. 콘텐츠 브라우저에서 C++ 클래스를 우클릭해 **레퍼런스 복사**한 뒤 작은따옴표 안쪽만 넣으면 틀릴 일이 없습니다.
- 대괄호 아래에 `변수이름=값`을 한 줄씩 적습니다.

코드에서 `SaveConfig();`를 호출하면 반대로 지금 변수 값이 설정 파일에 저장됩니다. 이때 저장되는 위치는 `Saved/Config/WindowsEditor/Game.ini`입니다. 음량이나 마우스 감도처럼 다음 실행 때도 유지되어야 하는 설정에 씁니다.

### 17-4 블루프린트 노출 지정자

헤더의 멤버 변수 선언입니다.

```cpp
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, meta = (AllowPrivateAccess = "true"))
USpringArmComponent* CameraBoom;

UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, meta = (AllowPrivateAccess = "true"))
UCameraComponent* FollowCamera;

UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, meta = (AllowPrivateAccess = "true"))
UInputMappingContext* DefaultMappingContext;

UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, meta = (AllowPrivateAccess = "true"))
UInputAction* MoveAction;
```

컴포넌트는 `VisibleAnywhere`, 에디터에서 지정할 에셋은 `EditAnywhere`입니다. Chapter 15에서 배운 규칙 그대로입니다. 나머지 지정자를 하나씩 봅니다.

`BlueprintReadOnly`와 `BlueprintReadWrite`의 차이는 직접 비교하면 분명합니다. 헤더에 변수 두 개를 추가하고 빌드합니다.

```cpp
UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Test)
int32 ReadOnlyInteger;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Test)
int32 ReadWriteInteger;
```

`BP_ThirdPersonCharacter`의 이벤트 그래프에서 우클릭해 검색하면 `Get Read Only Integer`와 `Get Read Write Integer`는 둘 다 나옵니다. `Set`으로 검색하면 `Set Read Write Integer`만 나옵니다.

| 지정자 | Get 노드 | Set 노드 |
|---|---|---|
| `BlueprintReadOnly` | 있다 | 없다 |
| `BlueprintReadWrite` | 있다 | 있다 |

`Category`는 디테일 패널과 노드 목록에서 묶일 분류 이름입니다. `Camera`와 `Input`처럼 엔진이 아는 단어는 한글 에디터에서 카메라, 입력으로 번역되어 보입니다.

`meta = (...)`는 추가 옵션을 넣는 자리입니다. `AllowPrivateAccess = "true"`는 `private` 영역에 선언한 변수도 블루프린트에 노출되게 합니다. C++에서 클래스의 접근 지정자를 적지 않으면 `private`이라 다른 클래스가 접근할 수 없습니다. 다른 C++ 클래스에는 숨기면서 블루프린트 자식에게만 보여 주고 싶을 때 이 옵션을 씁니다.

### 17-5 캡슐과 회전 설정

생성자의 첫 부분입니다.

```cpp
AMyCharacter::AMyCharacter()
{
	GetCapsuleComponent()->InitCapsuleSize(42.0f, 96.0f);

	bUseControllerRotationPitch = false;
	bUseControllerRotationYaw = false;
	bUseControllerRotationRoll = false;
```

**코드 해석**

- `ACharacter`를 상속받으면 캡슐, 메시, 캐릭터 무브먼트 컴포넌트가 이미 붙어 있습니다. `GetCapsuleComponent()`는 그중 루트인 캡슐을 가져옵니다.
- `InitCapsuleSize(반지름, 높이의 절반)`으로 부딪히는 범위의 크기를 정합니다.
- `bUseControllerRotation...` 세 줄은 컨트롤러의 회전을 캐릭터 몸에 그대로 적용할지 정합니다. 셋 다 `false`이므로 마우스를 움직여도 캐릭터 몸은 돌지 않고 카메라만 돕니다.

`Pitch`는 고개를 끄덕이는 방향, `Yaw`는 좌우로 도는 방향, `Roll`은 옆으로 기울이는 방향의 회전입니다. 일인칭 게임이라면 `bUseControllerRotationYaw`를 `true`로 두어 마우스 방향으로 몸이 돌게 합니다.

### 17-6 캐릭터 무브먼트 설정

```cpp
	GetCharacterMovement()->bOrientRotationToMovement = true;
	GetCharacterMovement()->RotationRate = FRotator(0.0f, 500.0f, 0.0f);

	GetCharacterMovement()->JumpZVelocity = 700.0f;
	GetCharacterMovement()->AirControl = 0.35f;
	GetCharacterMovement()->MaxWalkSpeed = 500.0f;
	GetCharacterMovement()->MinAnalogWalkSpeed = 20.0f;
	GetCharacterMovement()->BrakingDecelerationWalking = 2000.0f;
	GetCharacterMovement()->BrakingDecelerationFalling = 1500.0f;
```

| 변수 | 의미 |
|---|---|
| `bOrientRotationToMovement` | 움직이는 방향으로 캐릭터 몸을 돌린다 |
| `RotationRate` | 몸이 도는 빠르기. 1초에 도는 각도 |
| `JumpZVelocity` | 점프할 때 위로 솟는 속도 |
| `AirControl` | 공중에서 방향을 바꿀 수 있는 정도. `0`은 불가, `1`은 땅과 같음 |
| `MaxWalkSpeed` | 걷는 최대 속도 |
| `MinAnalogWalkSpeed` | 게임패드 스틱을 살짝 밀었을 때의 최소 속도 |
| `BrakingDecelerationWalking` | 땅에서 멈출 때 속도가 줄어드는 정도 |
| `BrakingDecelerationFalling` | 공중에서 수평 속도가 줄어드는 정도 |

이 값들은 `BP_ThirdPersonCharacter`에서 캐릭터 무브먼트 컴포넌트를 선택하면 디테일 패널에 그대로 보입니다. 코드에 적힌 값은 기본값이고, 블루프린트에서 덮어쓸 수 있습니다.

![BP_ThirdPersonCharacter에서 캐릭터 무브먼트 컴포넌트를 선택한 디테일 패널의 Max Walk Speed·Jump Z Velocity 항목](./img/17-character-movement-details.png)

### 17-7 스프링 암과 카메라

```cpp
	CameraBoom = CreateDefaultSubobject<USpringArmComponent>(TEXT("CameraBoom"));
	CameraBoom->SetupAttachment(RootComponent);
	CameraBoom->TargetArmLength = 400.0f;
	CameraBoom->bUsePawnControlRotation = true;

	FollowCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("FollowCamera"));
	FollowCamera->SetupAttachment(CameraBoom, USpringArmComponent::SocketName);
	FollowCamera->bUsePawnControlRotation = false;
}
```

3인칭 게임에서 캐릭터가 벽 가까이 가면 뒤따라오던 카메라가 벽 속으로 들어가 버립니다. **스프링 암**은 캐릭터와 카메라를 잇는 막대 역할의 컴포넌트로, 막대 중간에 벽이 있으면 길이를 줄여 카메라를 벽 앞으로 당겨 줍니다. 용수철처럼 늘었다 줄었다 해서 붙은 이름입니다.

**코드 해석**

- `TargetArmLength`는 막대의 길이입니다. 캐릭터 뒤 `400`만큼 떨어진 곳에 카메라가 놓입니다.
- `CameraBoom->bUsePawnControlRotation = true`는 마우스 입력에 따라 막대를 회전시킵니다. 막대가 돌면 끝에 달린 카메라가 캐릭터 주위를 돕니다.
- `SetupAttachment(CameraBoom, USpringArmComponent::SocketName)`의 두 번째 값은 **소켓** 이름입니다. 소켓은 컴포넌트에서 다른 것을 붙일 수 있도록 미리 정해 둔 자리이고, 스프링 암은 막대 끝에 소켓을 하나 가지고 있습니다.
- 카메라의 `bUsePawnControlRotation`은 `false`입니다. 막대가 이미 돌고 있으므로 카메라가 따로 돌 필요가 없습니다.

### 17-8 Enhanced Input 에셋

Chapter 12에서는 키보드 이벤트 노드로 입력을 받았습니다. 그 방식은 키가 코드에 고정되어 키 설정을 바꾸거나 게임패드를 지원하기 어렵습니다.

**Enhanced Input**은 "어떤 키를 눌렀는가"와 "어떤 행동을 하려는가"를 분리하는 입력 시스템입니다. 에셋 두 종류를 씁니다.

| 에셋 | 역할 | 예 |
|---|---|---|
| 입력 액션 | 행동 하나를 나타낸다 | `IA_Move`, `IA_Look`, `IA_Jump` |
| 입력 매핑 컨텍스트 | 어떤 키가 어떤 액션을 일으키는지 연결한다 | `IMC_Default` |

`콘텐츠/ThirdPerson/Input` 폴더에서 이 에셋들을 열어 봅니다. `IMC_Default`를 열면 `IA_Move`에 `W`, `A`, `S`, `D`와 게임패드 스틱이 함께 연결되어 있습니다.

![입력 매핑 컨텍스트 IMC_Default 에디터에서 IA_Move 액션에 W·A·S·D 키가 매핑된 화면](./img/17-mapping-context-wasd.png)

입력 액션에는 **값 타입**이 있습니다. 점프처럼 눌렀는지만 알면 되는 액션은 `Digital (bool)`, 이동처럼 앞뒤와 좌우 두 값이 필요한 액션은 `Axis2D`입니다.

헤더에서 `EditAnywhere`로 선언된 `DefaultMappingContext`와 `MoveAction` 변수에 어떤 에셋을 넣을지는 `BP_ThirdPersonCharacter`의 디테일 패널에서 지정되어 있습니다.

### 17-9 매핑 컨텍스트 등록과 액션 바인딩

에셋을 만들었다고 바로 동작하지는 않습니다. 두 가지 연결이 필요합니다.

첫째, 매핑 컨텍스트를 플레이어에게 등록합니다.

```cpp
void AMyCharacter::BeginPlay()
{
	Super::BeginPlay();

	if (APlayerController* PlayerController = Cast<APlayerController>(Controller))
	{
		if (UEnhancedInputLocalPlayerSubsystem* Subsystem =
			ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(PlayerController->GetLocalPlayer()))
		{
			Subsystem->AddMappingContext(DefaultMappingContext, 0);
		}
	}
}
```

**코드 해석**

- `Controller`는 이 캐릭터에 빙의한 컨트롤러입니다. `Cast<APlayerController>`는 블루프린트의 캐스트 노드와 같은 일을 합니다. 플레이어 컨트롤러가 아니면 `nullptr`이 되어 `if` 안으로 들어가지 않습니다.
- `GetLocalPlayer()`는 이 컴퓨터에서 실제로 조작하는 플레이어를 가져옵니다.
- `UEnhancedInputLocalPlayerSubsystem`은 그 플레이어의 입력을 관리하는 객체입니다.
- `AddMappingContext(매핑 컨텍스트, 우선순위)`로 등록합니다. 이 줄이 실행되어야 키 입력이 액션으로 바뀝니다.

둘째, 액션이 일어났을 때 호출할 함수를 연결합니다. 이 연결을 **바인딩**이라고 합니다.

```cpp
void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	if (UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(PlayerInputComponent))
	{
		EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Started, this, &ACharacter::Jump);
		EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Completed, this, &ACharacter::StopJumping);

		EnhancedInputComponent->BindAction(MoveAction, ETriggerEvent::Triggered, this, &AMyCharacter::Move);
		EnhancedInputComponent->BindAction(LookAction, ETriggerEvent::Triggered, this, &AMyCharacter::Look);
	}
}
```

**코드 해석**

- `SetupPlayerInputComponent`는 컨트롤러가 빙의할 때 엔진이 호출해 주는 함수입니다. 입력 연결은 여기에 모읍니다.
- `BindAction(액션, 시점, 대상 객체, 함수 주소)` 형태입니다.
- 시점 `Started`는 누르기 시작한 순간, `Triggered`는 누르고 있는 동안 매 프레임, `Completed`는 뗀 순간입니다.
- `Jump`와 `StopJumping`은 `ACharacter`에 이미 만들어져 있는 함수입니다.

### 17-10 이동과 시점 회전

바인딩된 두 함수입니다.

```cpp
void AMyCharacter::Move(const FInputActionValue& Value)
{
	FVector2D MovementVector = Value.Get<FVector2D>();

	if (Controller != nullptr)
	{
		const FRotator Rotation = Controller->GetControlRotation();
		const FRotator YawRotation(0.0f, Rotation.Yaw, 0.0f);

		const FVector ForwardDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
		const FVector RightDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);

		AddMovementInput(ForwardDirection, MovementVector.Y);
		AddMovementInput(RightDirection, MovementVector.X);
	}
}

void AMyCharacter::Look(const FInputActionValue& Value)
{
	FVector2D LookAxisVector = Value.Get<FVector2D>();

	if (Controller != nullptr)
	{
		AddControllerYawInput(LookAxisVector.X);
		AddControllerPitchInput(LookAxisVector.Y);
	}
}
```

**코드 해석**

- `Value.Get<FVector2D>()`는 액션이 전달한 값 두 개를 꺼냅니다. 이동이라면 X가 좌우, Y가 앞뒤입니다.
- `GetControlRotation()`은 컨트롤러가 바라보는 방향입니다. 그중 `Yaw`만 남겨 바닥과 평행한 방향을 만듭니다. 카메라가 위를 보고 있어도 캐릭터는 바닥을 따라 움직여야 하기 때문입니다.
- `GetUnitAxis(EAxis::X)`는 그 방향을 기준으로 한 앞쪽, `EAxis::Y`는 오른쪽을 가리키는 길이 1의 벡터입니다.
- `AddMovementInput(방향, 세기)`는 "이 방향으로 가고 싶다"고 캐릭터 무브먼트에 전달합니다. 실제 이동, 가속, 충돌 처리는 캐릭터 무브먼트가 합니다.
- `AddControllerYawInput`과 `AddControllerPitchInput`은 컨트롤러의 바라보는 방향을 돌립니다. 17-7에서 스프링 암이 이 회전을 따라가도록 설정했으므로 카메라가 돕니다.

키 입력부터 캐릭터가 움직이기까지의 흐름을 정리하면 다음과 같습니다.

1. 플레이어가 `W`를 누른다.
2. 매핑 컨텍스트가 `W`를 `IA_Move` 액션으로 바꾼다.
3. 바인딩에 따라 `Move` 함수가 호출된다.
4. `AddMovementInput`으로 방향이 전달된다.
5. 캐릭터 무브먼트가 캐릭터를 이동시킨다.

### 17-11 따라하기 — 템플릿 고쳐 보기

1. 프로젝트에 C++ 삼인칭 피처를 추가하고 빌드한다.
2. `MyCustomProperty`를 `DefaultGame.ini`에서 읽어 로그로 확인한다.
3. `ReadOnlyInteger`와 `ReadWriteInteger`를 추가해 블루프린트에서 Set 노드 유무를 확인한다.
4. 생성자에서 `MaxWalkSpeed`를 `1000`, `JumpZVelocity`를 `1200`으로 바꿔 빌드하고 플레이한다.
5. `TargetArmLength`를 `150`과 `800`으로 바꿔 카메라 거리 차이를 확인한다.
6. `bUseControllerRotationYaw`를 `true`, `bOrientRotationToMovement`를 `false`로 바꿔 마우스 방향으로 몸이 도는 조작감을 확인한 뒤 되돌린다.
7. `IMC_Default`에서 점프 키를 스페이스 바에서 다른 키로 바꿔 본다.

값을 바꿨는데 반영되지 않으면 `BP_ThirdPersonCharacter`가 같은 값을 덮어쓰고 있는지 확인합니다. 블루프린트의 디테일 패널에서 값 옆의 되돌리기 화살표를 누르면 C++ 기본값으로 돌아갑니다.

### 연습문제

1. 문제: `FObjectFinder`와 `FClassFinder`의 차이를 한 문장으로 쓰세요.
2. 문제: 캐릭터가 벽에 붙었을 때 카메라가 벽 속으로 들어가지 않게 해 주는 컴포넌트 이름과, 카메라 거리를 정하는 변수 이름을 쓰세요.
3. 문제: 입력 액션 에셋을 만들고 매핑 컨텍스트에 키도 연결했는데 캐릭터가 움직이지 않을 때 확인할 코드 두 곳을 쓰세요.  
   - 조건: 함수 이름으로 답할 것

### 정답 포인트

- `FObjectFinder`는 에셋을, `FClassFinder`는 클래스를 찾는다. 스프링 암 컴포넌트, `TargetArmLength`. `AddMappingContext` 호출과 `BindAction` 호출.

---

[상위 문서로 돌아가기](./README.md)
