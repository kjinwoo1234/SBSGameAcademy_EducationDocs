# Chapter 13 언리얼 C++ 첫걸음 — 부유하는 큐브

## 학습 목표

- 블루프린트와 C++를 어떤 기준으로 나눠 쓰는지 설명할 수 있다.
- C++ 프로젝트에 액터 클래스를 추가하고 빌드해 레벨에 배치할 수 있다.
- 생성자, `BeginPlay`, `Tick`이 각각 언제 호출되는지 구분할 수 있다.
- C++ 클래스를 부모로 블루프린트를 만들어 겉모습을 바꿀 수 있다.

## 본문

### 13-1 왜 C++를 함께 쓰는가

Part 01에서는 블루프린트만으로 액터를 움직였습니다. 블루프린트로도 게임 하나를 완성할 수 있는데 왜 C++를 배울까요?

블루프린트에서 조건을 검사하는 노드를 네 개만 이어 붙여도 선이 화면 밖으로 뻗어 나갑니다. 로직이 길어질수록 어느 선이 어디로 가는지 읽기 어려워지고, 계산이 많은 기능은 C++보다 느립니다.

반대로 C++로 큐브 하나를 액터에 붙이려면 헤더에 변수를 선언하고, 생성자에서 만들고, 붙이고, 모형 파일의 경로를 글자로 적어야 합니다. 블루프린트에서는 버튼 한 번이면 끝나는 일입니다.

그래서 언리얼 개발에서는 두 가지를 나눠 씁니다.

| 맡기는 일 | 도구 | 이유 |
|---|---|---|
| 기능과 규칙, 복잡한 계산 | C++ | 긴 로직을 읽기 쉽고 실행이 빠르다 |
| 겉모습 지정, 수치 조절, 간단한 연결 | 블루프린트 | 눈으로 보며 빠르게 바꿀 수 있다 |

캐릭터가 할 수 있는 동작은 C++ 클래스에 만들고, 그 클래스를 부모로 블루프린트를 만들어 모형과 수치를 정하는 방식이 가장 흔합니다. 이 장에서 그 흐름을 처음부터 끝까지 한 번 따라갑니다.

### 13-2 C++ 프로젝트 만들기

언리얼 C++ 코드는 Visual Studio에서 작성하고 빌드합니다. Visual Studio Installer에서 **C++를 사용한 게임 개발** 워크로드가 설치되어 있어야 합니다.

1. 프로젝트 브라우저에서 **게임 → 기본**을 고른다.
2. 프로젝트 디폴트에서 **C++**를 고르고 **시작용 콘텐츠**에 체크한다.
3. 위치와 이름을 정하고 **생성**을 누른다. 이 과정의 예제 프로젝트 이름은 `Practice`이다.
4. Visual Studio와 언리얼 에디터가 함께 열린다.
5. 에디터에서 `콘텐츠/StarterContent/Maps/Minimal_Default` 레벨을 연다.

프로젝트 폴더에 `Source` 폴더가 생겼습니다. C++ 코드는 모두 이 폴더 아래에 들어갑니다.

### 13-3 C++ 클래스 추가

1. 에디터 메뉴 바에서 **툴 → 새 C++ 클래스**를 누른다.
2. 부모 클래스로 **액터**를 고르고 **다음**을 누른다.
3. 이름을 `FloatingActor`로 짓고 **클래스 생성**을 누른다.
4. Visual Studio에 파일이 바뀌었다는 창이 뜨면 **모두 다시 로드**를 누른다.

`Source/Practice` 폴더에 `FloatingActor.h`와 `FloatingActor.cpp`가 생깁니다. 클래스 이름은 `FloatingActor`로 지었지만 코드 안에서는 `AFloatingActor`로 되어 있습니다. 액터를 상속받는 클래스 이름 앞에는 `A`를 붙이는 것이 언리얼의 규칙이고, 엔진이 자동으로 붙여 줍니다. 이름 규칙은 Chapter 14에서 정리합니다.

### 13-4 자동 생성된 함수 세 개

생성된 `FloatingActor.cpp`에는 함수 세 개가 미리 들어 있습니다.

```cpp
AFloatingActor::AFloatingActor()
{
	PrimaryActorTick.bCanEverTick = true;
}

void AFloatingActor::BeginPlay()
{
	Super::BeginPlay();
}

void AFloatingActor::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);
}
```

**코드 해석**

- `AFloatingActor()`는 생성자입니다. 이 클래스의 기본값을 정하는 곳으로 씁니다. 컴포넌트를 만들어 붙이거나 변수의 처음 값을 넣습니다.
- `BeginPlay`는 게임이 시작되어 액터가 레벨에 나타났을 때 한 번 호출됩니다. 블루프린트의 `Event BeginPlay`와 같습니다.
- `Tick`은 매 프레임 호출됩니다. 블루프린트의 `Event Tick`과 같습니다. 매개변수 `DeltaTime`은 이전 프레임부터 지금 프레임까지 걸린 시간이고, 블루프린트의 Delta Seconds와 같은 값입니다.
- `PrimaryActorTick.bCanEverTick = true`는 이 액터의 `Tick`을 호출하겠다는 설정입니다. `false`로 바꾸면 `Tick`이 호출되지 않습니다.
- `Super::`는 부모 클래스의 같은 함수를 먼저 호출한다는 뜻입니다. 지우면 엔진이 해 주던 기본 처리가 빠지므로 그대로 둡니다.

생성자와 `BeginPlay`는 둘 다 "처음에 한 번"이라 헷갈리기 쉽습니다. 생성자는 에디터에서 블루프린트를 열 때도 호출되는 기본값 설정용이고, `BeginPlay`는 실제 게임이 시작됐을 때만 호출됩니다.

### 13-5 메시 컴포넌트 붙이기

액터에 큐브 모양을 붙입니다. 먼저 헤더 파일의 클래스 안, `public:` 아래에 컴포넌트를 가리킬 포인터를 선언합니다.

```cpp
// FloatingActor.h
UPROPERTY(VisibleAnywhere)
UStaticMeshComponent* VisualMesh;
```

`UStaticMeshComponent`는 스태틱 메시를 화면에 보여 주는 컴포넌트입니다. 블루프린트에서 큐브 컴포넌트를 추가했을 때 붙던 것과 같은 종류입니다. 윗줄의 `UPROPERTY(VisibleAnywhere)`는 이 변수를 에디터의 디테일 패널에서 볼 수 있게 해 주는 표시입니다. 자세한 의미는 Chapter 14에서 배우고, 지금은 그대로 적습니다.

다음으로 생성자에서 컴포넌트를 만들고 큐브 모형을 지정합니다.

```cpp
// FloatingActor.cpp
AFloatingActor::AFloatingActor()
{
	PrimaryActorTick.bCanEverTick = true;

	VisualMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"));
	VisualMesh->SetupAttachment(RootComponent);

	static ConstructorHelpers::FObjectFinder<UStaticMesh> CubeVisualAsset(
		TEXT("/Game/StarterContent/Shapes/Shape_Cube.Shape_Cube"));

	if (CubeVisualAsset.Succeeded())
	{
		VisualMesh->SetStaticMesh(CubeVisualAsset.Object);
		VisualMesh->SetRelativeLocation(FVector(0.0f, 0.0f, 0.0f));
	}
}
```

**코드 해석**

- `CreateDefaultSubobject<타입>(TEXT("이름"))`은 컴포넌트를 만드는 함수입니다. 언리얼에서는 `new` 대신 이 함수를 씁니다. 엔진이 메모리를 관리해 주므로 `delete`를 직접 호출하지 않습니다. 괄호 안의 이름은 같은 액터 안에서 겹치면 안 됩니다.
- `TEXT("...")`는 언리얼에서 문자열을 적을 때 감싸는 매크로입니다. 한글 등 여러 나라 글자를 안전하게 다루기 위해 씁니다.
- `SetupAttachment`는 컴포넌트를 다른 컴포넌트 아래에 붙입니다.
- `ConstructorHelpers::FObjectFinder<타입>`은 에셋의 경로로 에셋을 찾아오는 도구입니다. 생성자 안에서만 쓸 수 있습니다.
- `Succeeded()`는 찾기에 성공했는지 알려 줍니다. 성공했을 때만 `SetStaticMesh`로 찾은 모형을 지정합니다.
- `SetRelativeLocation`은 부모를 기준으로 한 컴포넌트의 위치를 정합니다. `FVector`는 X, Y, Z 세 값을 담는 언리얼의 벡터 타입입니다.

경로 문자열은 직접 외워 적지 않습니다. 콘텐츠 브라우저에서 에셋을 우클릭하고 **레퍼런스 복사**를 누르면 엔진이 그 에셋을 찾을 때 쓰는 주소가 복사됩니다. 복사된 글자 중 작은따옴표 안쪽만 `TEXT("")` 안에 넣습니다. 같은 메뉴의 **파일 경로 복사**는 하드디스크에서의 실제 파일 위치라서 여기에 쓰면 안 됩니다.

### 13-6 Tick에서 부유와 회전

위아래로 떠다니며 천천히 도는 동작을 `Tick`에 넣습니다.

```cpp
void AFloatingActor::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);

	FVector NewLocation = GetActorLocation();
	FRotator NewRotation = GetActorRotation();

	float RunningTime = GetGameTimeSinceCreation();
	float DeltaHeight = FMath::Sin(RunningTime + DeltaTime) - FMath::Sin(RunningTime);
	NewLocation.Z += DeltaHeight * 20.0f;

	float DeltaRotation = DeltaTime * 20.0f;
	NewRotation.Yaw += DeltaRotation;

	SetActorLocationAndRotation(NewLocation, NewRotation);
}
```

**코드 해석**

- `GetActorLocation`과 `GetActorRotation`은 액터의 지금 위치와 회전을 가져옵니다. `FRotator`는 회전을 담는 타입이고 `Yaw`는 위에서 내려다봤을 때 도는 방향입니다.
- `GetGameTimeSinceCreation`은 이 액터가 만들어진 뒤 흐른 시간을 초 단위로 돌려줍니다. 계속 커지는 값입니다.
- `FMath::Sin`은 사인 함수입니다. 넣는 값이 계속 커져도 결과는 `-1`과 `1` 사이를 부드럽게 오르내립니다. 이 성질 덕분에 위아래로 왕복하는 움직임이 만들어집니다.
- `DeltaHeight`는 지금 프레임의 사인 값과 다음 프레임의 사인 값의 차이입니다. 이번 프레임에 움직일 높이만큼만 더합니다.
- `DeltaTime * 20.0f`는 Chapter 09에서 배운 것과 같습니다. 컴퓨터 성능과 관계없이 1초에 20도씩 돌게 합니다.
- `SetActorLocationAndRotation`은 계산한 위치와 회전을 액터에 한 번에 적용합니다.

### 13-7 빌드하기

작성한 코드를 실행 가능한 형태로 바꾸는 일을 **빌드**라고 합니다. 빌드를 해야 에디터가 새 클래스를 알아봅니다.

방법은 두 가지입니다.

| 방법 | 순서 | 특징 |
|---|---|---|
| 에디터를 끄고 빌드 | 에디터 종료 → Visual Studio에서 `Ctrl + Shift + B` → `F5`로 에디터 다시 실행 | 느리지만 확실하다 |
| 라이브 코딩 | 에디터를 켠 채 `Ctrl + Alt + F11` | 빠르지만 헤더 파일을 바꿨을 때 반영이 불완전할 수 있다 |

헤더 파일에 변수나 함수를 새로 선언했을 때는 에디터를 끄고 빌드하는 방법을 권합니다. `.cpp` 파일 안의 계산만 고쳤을 때는 라이브 코딩으로 충분합니다.

빌드가 끝나면 콘텐츠 브라우저의 `C++ 클래스/Practice` 폴더에 `FloatingActor`가 보입니다. 레벨로 끌어다 놓고 플레이하면 큐브가 떠다니며 회전합니다.

![콘텐츠 브라우저의 C++ 클래스 폴더에 FloatingActor가 보이고 레벨에 배치된 큐브가 떠 있는 화면](./img/13-floating-actor-in-level.png)

### 13-8 속도를 디테일 패널에서 조절하기

코드에 적은 `20.0f`를 바꾸려면 매번 다시 빌드해야 합니다. 값을 변수로 빼서 에디터에서 조절할 수 있게 만듭니다.

헤더에 변수 두 개를 추가합니다.

```cpp
// FloatingActor.h
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "FloatingActor")
float FloatSpeed = 20.0f;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "FloatingActor")
float RotationSpeed = 20.0f;
```

`Tick`에서 숫자 `20.0f` 두 곳을 변수로 바꿉니다.

```cpp
NewLocation.Z += DeltaHeight * FloatSpeed;

float DeltaRotation = DeltaTime * RotationSpeed;
```

**코드 해석**

- `EditAnywhere`는 디테일 패널에서 이 값을 수정할 수 있게 합니다.
- `BlueprintReadWrite`는 블루프린트 그래프에서 이 값을 읽고 쓸 수 있게 합니다.
- `Category = "FloatingActor"`는 디테일 패널에서 이 값이 표시될 묶음의 이름입니다.

빌드한 뒤 레벨의 `FloatingActor`를 선택하면 디테일 패널에 **Float Speed**와 **Rotation Speed** 칸이 생깁니다. Chapter 12에서 블루프린트 변수에 인스턴스 편집가능을 켰던 것과 같은 결과를 C++에서 만든 것입니다.

### 13-9 블루프린트와 연계

마지막으로 이 C++ 클래스를 부모로 블루프린트를 만들어 모양을 바꿉니다.

1. 콘텐츠 브라우저에서 `FloatingActor`를 우클릭하고 **FloatingActor 기반 블루프린트 클래스 생성**을 누른다.
2. 이름을 `BP_FloatingActor`로 짓고 콘텐츠 폴더에 저장한다.
3. 열린 블루프린트 에디터의 컴포넌트 탭에서 `VisualMesh`를 선택한다.
4. 디테일 패널의 **스태틱 메시**를 다른 모형으로 바꾼다.
5. 컴파일하고 `BP_FloatingActor`를 레벨에 놓는다.

![BP_FloatingActor 블루프린트 에디터에서 VisualMesh 컴포넌트의 스태틱 메시를 다른 모형으로 바꾼 화면](./img/13-bp-floating-actor-mesh.png)

떠다니고 회전하는 동작은 C++에 있고, 어떤 모형으로 보일지는 블루프린트에서 정했습니다. 코드에서는 경로를 글자로 적어야 했던 일을 블루프린트에서는 목록에서 고르기만 하면 됩니다. 13-1에서 말한 역할 분담이 이것입니다.

### 13-10 따라하기 — 부유하는 큐브

1. C++ 프로젝트를 만들고 `FloatingActor` 클래스를 추가한다.
2. `VisualMesh`를 선언하고 생성자에서 큐브를 지정한다.
3. `Tick`에 부유와 회전 코드를 넣고 빌드한다.
4. 레벨에 배치해 움직임을 확인한다.
5. `FloatSpeed`와 `RotationSpeed`를 추가하고 디테일 패널에서 값을 바꿔 본다.
6. `BP_FloatingActor`를 만들어 모형을 바꾸고, 레벨에 여러 개 놓아 속도를 서로 다르게 준다.

이 장에서는 설명 없이 그대로 적은 `UPROPERTY`와 이름 앞의 `A`, `U`, `F`가 무엇인지 Chapter 14에서 배웁니다.

### 연습문제

1. 문제: 생성자와 `BeginPlay`의 쓰임 차이를 한 줄씩 쓰세요.  
   - 조건: 기본값 설정과 게임 시작을 각각 언급할 것
2. 문제: `FObjectFinder`에 넣을 에셋 경로를 얻는 방법을 한 문장으로 쓰세요.  
   - 조건: 콘텐츠 브라우저의 메뉴 이름을 쓸 것
3. 문제: `Tick`에서 `NewRotation.Yaw += 20.0f;`라고만 쓰면 생기는 문제를 한 문장으로 쓰세요.  
   - 조건: `DeltaTime`을 언급할 것

### 정답 포인트

- 생성자는 컴포넌트와 기본값 설정, `BeginPlay`는 게임 시작 시 한 번 실행할 코드. 에셋을 우클릭해 레퍼런스 복사. `DeltaTime`을 곱하지 않아 프레임이 많은 컴퓨터에서 더 빨리 돈다.

---

[상위 문서로 돌아가기](./README.md)
