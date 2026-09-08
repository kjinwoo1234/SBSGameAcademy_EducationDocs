# Chapter 02 컨테이너·FPS 기초

## 학습 목표

- `TArray`·`TMap`·`TSet`의 쓰임을 구분해 쓸 수 있다.
- `check`·`ensure` 어서트와 `IsValid`의 역할을 구분할 수 있다.
- 액터·컴포넌트 라이프사이클 호출 순서를 설명할 수 있다.
- Enhanced Input으로 액션을 바인딩하고 캐릭터 이동을 연결할 수 있다.

## 본문

### 02-1 언리얼 컨테이너

C++ 표준 컨테이너 대신 언리얼 코드에서는 엔진·리플렉션과 맞추기 쉬운 타입을 자주 씁니다.

| 타입 | 역할 | 예 |
|---|---|---|
| **`TArray`** | 순서 있는 동적 배열 | 적 목록, 순찰 지점 |
| **`TMap`** | 키→값 | 아이템 ID→개수, 이름→점수 |
| **`TSet`** | 중복 없는 집합 | 이미 맞춘 액터, 방문한 지점 |

```cpp
TArray<AActor*> Targets;
Targets.Add(SomeActor);
for (AActor* Target : Targets)
{
	if (IsValid(Target))
	{
		// 처리
	}
}

TMap<FName, int32> AmmoByWeapon;
AmmoByWeapon.Add(TEXT("Rifle"), 30);

TSet<AActor*> AlreadyHit;
AlreadyHit.Add(HitActor);
if (AlreadyHit.Contains(HitActor))
{
	// 중복 처리 방지
}
```

미니 과제: `TArray`에 순찰 지점 액터를 넣고, `TMap`으로 지점 이름→대기 초를 저장한 뒤, 순회하며 로그로 출력해 보세요.

### 02-2 어서트

**어서트**는 “이 조건이 깨지면 개발 중 바로 멈추게” 하는 검사입니다.

| 매크로 | 느낌 |
|---|---|
| `check` | 반드시 참이어야 함. 깨지면 개발 빌드에서 중단 |
| `ensure` / `ensureMsgf` | 깨져도 가능하면 계속. 로그·디버그에 남김 |

```cpp
check(Mesh != nullptr);
ensureMsgf(FloatSpeed > 0.0f, TEXT("FloatSpeed should be positive"));
```

릴리스 빌드에서는 동작이 달라질 수 있습니다. 플레이어에게 보여줄 오류 처리와 개발용 검사를 구분해 둡니다. 이 장 이후 예제에서도 포인터를 쓰기 전에 `IsValid`나 null 검사를 습관처럼 넣습니다.

### 02-3 컴포넌트 라이프사이클

액터는 **컴포넌트**를 묶어 기능을 나눕니다. 메시, 이동, 카메라가 각각 컴포넌트인 식입니다.

| 시점 | 대표 함수 | 하는 일 |
|---|---|---|
| 생성 | 생성자, `CreateDefaultSubobject` | 기본 컴포넌트 트리 구성 |
| 배치·로드 후 | `BeginPlay` | 게임 시작 시 초기화 |
| 매 프레임 | `Tick` | 지속 갱신. 필요 없으면 끄는 편이 낫다 |
| 제거 | `EndPlay` | 타이머·바인딩 정리 |

캐릭터는 `ACharacter`를 상속하면 `CharacterMovementComponent`가 기본으로 붙습니다. 직접 `AddActorWorldOffset`만 쓰기보다, 이동 입력을 이동 컴포넌트에 넘기는 방식이 FPS 기초에 맞습니다.

### 02-4 Enhanced Input 개요

예전 프로젝트 설정의 Action/Axis Mapping 대신, 최근 템플릿은 **Enhanced Input**을 씁니다.

| 에셋 | 역할 |
|---|---|
| Input Action | “점프”, “이동” 같은 논리적 행동 |
| Input Mapping Context | 키·패드 버튼을 액션에 연결 |
| Input Modifier / Trigger | 값 가공, 눌림·토글 조건 |

![콘텐츠 브라우저의 Input Action·Input Mapping Context 에셋 아이콘과 이름 예시](./img/02-enhanced-input-assets.png)

**플레이어 컨트롤러**는 “이 플레이어의 입력·시야·UI를 담당하는” 액터입니다. 캐릭터(폰)가 몸이라면, 컨트롤러는 조종석에 가깝습니다.

**로컬 플레이어**는 “이 PC에서 실제로 조작하는 그 플레이어”를 엔진이 다루는 객체입니다. Enhanced Input의 Mapping Context는 **그 로컬 플레이어의 입력 서브시스템**에 등록해야 키가 액션으로 전달됩니다.

### 02-5 Input Binding과 캐릭터 이동

Third Person 또는 First Person 템플릿을 쓰면 이동 골격이 이미 있습니다. 직접 최소 연결만 본다면 흐름은 다음과 같습니다.

1. `IA_Move` 같은 Input Action을 만든다. Value Type은 Axis2D 또는 Axis3D.
2. Mapping Context에 WASD·스틱을 연결한다.

![Input Mapping Context 에디터에서 WASD 키를 IA_Move 액션에 매핑한 화면](./img/02-mapping-context-wasd.png)

3. `BeginPlay`에서 로컬 플레이어의 Enhanced Input 서브시스템에 Mapping Context를 추가한다.
4. `SetupPlayerInputComponent`에서 Input Action에 `BindAction`으로 콜백을 묶는다.
5. 콜백에서 `AddMovementInput`에 전방·우측 벡터와 축 값을 넘긴다.

```cpp
void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);

	if (UEnhancedInputComponent* EIC = Cast<UEnhancedInputComponent>(PlayerInputComponent))
	{
		EIC->BindAction(MoveAction, ETriggerEvent::Triggered, this, &AMyCharacter::Move);
		EIC->BindAction(LookAction, ETriggerEvent::Triggered, this, &AMyCharacter::Look);
	}
}

void AMyCharacter::Move(const FInputActionValue& Value)
{
	const FVector2D Axis = Value.Get<FVector2D>();
	if (Controller == nullptr)
	{
		return;
	}

	const FRotator YawRot(0.0f, Controller->GetControlRotation().Yaw, 0.0f);
	const FVector Forward = FRotationMatrix(YawRot).GetUnitAxis(EAxis::X);
	const FVector Right = FRotationMatrix(YawRot).GetUnitAxis(EAxis::Y);

	AddMovementInput(Forward, Axis.Y);
	AddMovementInput(Right, Axis.X);
}
```

**코드 해석**

- `BindAction`이 Input Binding의 핵심입니다. 액션이 트리거될 때 멤버 함수가 호출됩니다.
- 카메라 yaw만 반영해 바닥 이동 방향을 정합니다.
- `AddMovementInput`은 `CharacterMovement`가 속도와 충돌을 처리하게 맡깁니다.

마우스 룩은 `AddControllerYawInput` / `AddControllerPitchInput`에 연결합니다.

### 02-6 따라하기 — FPS 이동 프로토타입

1. First Person 또는 Third Person C++ 템플릿으로 프로젝트를 연다.
2. 플레이해 WASD·마우스가 동작하는지 확인한다.
3. `IA_Move`·Mapping Context가 콘텐츠 브라우저에 있는지 찾는다.
4. 캐릭터의 `SetupPlayerInputComponent`에 `BindAction`이 있는지 코드를 읽는다.
5. 이동 속도를 Character Movement 디테일에서 바꿔 체감을 확인한다.

![캐릭터 블루프린트 디테일 패널의 Character Movement Max Walk Speed 항목](./img/02-character-movement-details.png)

6. `TArray`에 액터 두 개를 넣고 `IsValid` 검사 후 이름을 로그로 출력하는 짧은 코드를 `BeginPlay`에 넣어 본다.

발사·트레이스는 Chapter 03에서 다룹니다.

### 연습문제

1. 문제: `TMap`이 `TArray`보다 잘 맞는 상황을 한 문장으로 쓰세요.  
2. 문제: `BeginPlay`와 `Tick`의 차이를 두 문장으로 쓰세요.  
3. 문제: `BindAction`이 하는 일을 한 문장으로 쓰세요.

### 정답 포인트

- 키로 바로 찾을 때 `TMap`. BeginPlay=시작 1회, Tick=매 프레임. BindAction=액션→함수 연결.

---

[상위 문서로 돌아가기](./README.md)
