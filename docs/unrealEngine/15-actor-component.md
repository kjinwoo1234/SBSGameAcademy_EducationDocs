# Chapter 15 액터와 컴포넌트

## 학습 목표

- 루트 컴포넌트, 씬 컴포넌트, 액터 컴포넌트의 차이를 설명할 수 있다.
- 자동 생성된 액터 클래스 코드의 각 줄이 하는 일을 설명할 수 있다.
- C++로 컴포넌트 여러 개를 만들어 계층으로 붙일 수 있다.
- 객체 유형과 값 유형에 맞는 `UPROPERTY` 지정자를 고를 수 있다.

## 본문

### 15-1 액터가 하는 일

Chapter 02에서 액터를 "레벨에 놓을 수 있는 모든 물체"라고 했습니다. 액터가 게임에서 하는 일은 크게 세 가지로 나뉩니다.

| 기능 | 내용 |
|---|---|
| 시각 | 플레이어에게 어떻게 보일 것인가 |
| 물리 | 다른 액터와 어떻게 부딪히고 반응할 것인가 |
| 움직임 | 어떻게 이동할 것인가 |

액터 혼자서는 이 일을 하지 못합니다. 각 기능을 담당하는 컴포넌트를 붙여야 합니다. 액터는 컴포넌트를 담는 그릇이고, 실제 일은 컴포넌트가 합니다.

일인칭 템플릿의 캐릭터 블루프린트를 열어 컴포넌트 탭을 보면 다섯 개의 컴포넌트가 보입니다.

| 컴포넌트 | 담당 |
|---|---|
| 캡슐 컴포넌트 | 캐릭터가 부딪히는 범위 |
| 화살표 컴포넌트 | 캐릭터의 앞 방향을 에디터에서 표시 |
| 메시 | 캐릭터의 모형 |
| 카메라 | 화면을 어디서 볼 것인가 |
| 캐릭터 무브먼트 | 걷기, 점프, 낙하 같은 움직임 |

### 15-2 루트 컴포넌트와 씬 컴포넌트

컴포넌트 탭은 나무 모양의 계층으로 되어 있습니다. 맨 위에 있는 컴포넌트를 **루트 컴포넌트**라고 합니다. 액터의 위치와 회전은 루트 컴포넌트의 위치와 회전입니다. 빈 액터 블루프린트는 `DefaultSceneRoot`가 루트이고, 캐릭터는 캡슐 컴포넌트가 루트입니다.

컴포넌트는 트랜스폼을 가지는지에 따라 두 종류로 나뉩니다.

| 종류 | 트랜스폼 | 계층 | 예 |
|---|---|---|---|
| 씬 컴포넌트 | 있다 | 루트 아래에 붙는다 | 메시, 카메라, 캡슐, 라이트 |
| 액터 컴포넌트 | 없다 | 계층에 속하지 않는다 | 캐릭터 무브먼트 |

씬 컴포넌트는 위치가 있어서 화면 어딘가에 놓이는 부품입니다. 부모 컴포넌트가 움직이면 함께 움직입니다. 액터 컴포넌트는 위치 없이 기능만 제공하는 부품입니다. 컴포넌트 탭에서 가로줄 위쪽에 있는 것이 씬 컴포넌트, 아래쪽에 있는 것이 액터 컴포넌트입니다.

![캐릭터 블루프린트의 컴포넌트 탭: 가로줄 위에 캡슐·화살표·메시·카메라, 아래에 캐릭터 무브먼트가 나뉘어 표시된 화면](./img/15-components-tab-scene-vs-actor.png)

### 15-3 액터 클래스 코드 해부

Chapter 13에서 자동 생성된 코드를 그대로 썼습니다. 이번에는 새 액터 클래스 `MyActor`를 만들고 헤더를 한 줄씩 읽습니다. **툴 → 새 C++ 클래스 → 액터**로 만듭니다.

```cpp
// MyActor.h
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "MyActor.generated.h"

UCLASS()
class PRACTICE_API AMyActor : public AActor
{
	GENERATED_BODY()

public:
	AMyActor();

protected:
	virtual void BeginPlay() override;

public:
	virtual void Tick(float DeltaTime) override;
};
```

**코드 해석**

- `CoreMinimal.h`는 언리얼 코드에 꼭 필요한 최소한의 타입을 모아 둔 헤더입니다. `FVector`, `FString`, `TArray` 같은 기본 타입이 여기에 들어 있습니다. 그 밖의 기능은 필요한 헤더를 따로 `#include`해야 합니다.
- `GameFramework/Actor.h`는 부모 클래스 `AActor`가 선언된 헤더입니다. 상속받으려면 부모의 선언을 알아야 하므로 포함합니다.
- `MyActor.generated.h`는 리플렉션을 위해 엔진이 자동으로 만드는 헤더입니다. 반드시 `#include` 중 맨 마지막에 와야 합니다.
- `PRACTICE_API`는 프로젝트 이름을 대문자로 쓴 뒤 `_API`를 붙인 매크로입니다. 이 클래스를 다른 모듈에서도 쓸 수 있게 공개한다는 표시입니다. 프로젝트 이름이 다르면 이 부분도 달라지므로, 새로 만든 헤더에 이미 적혀 있는 것을 그대로 둡니다.
- `: public AActor`는 `AActor`를 상속받는다는 뜻입니다.

여기서 **모듈**은 코드를 묶어 빌드하는 단위입니다. 프로젝트를 만들면 프로젝트 이름과 같은 모듈 하나가 기본으로 생기고, 우리가 만드는 클래스는 모두 그 안에 들어갑니다.

### 15-4 컴포넌트 두 개 붙이기

`MyActor`에 메시 컴포넌트 두 개를 붙여 계층을 직접 만듭니다. 헤더의 클래스 안에 포인터를 선언합니다.

```cpp
// MyActor.h
public:
	UPROPERTY(VisibleAnywhere)
	UStaticMeshComponent* Mesh1;

	UPROPERTY(VisibleAnywhere)
	UStaticMeshComponent* Mesh2;
```

생성자에서 만들고 붙입니다.

```cpp
// MyActor.cpp
AMyActor::AMyActor()
{
	PrimaryActorTick.bCanEverTick = true;

	Mesh1 = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh1"));
	Mesh2 = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh2"));

	RootComponent = Mesh1;
	Mesh2->SetupAttachment(Mesh1);

	Mesh2->SetRelativeLocation(FVector(0.0f, 0.0f, -50.0f));
}
```

**코드 해석**

- `RootComponent = Mesh1`은 `Mesh1`을 이 액터의 루트 컴포넌트로 정합니다.
- `Mesh2->SetupAttachment(Mesh1)`은 `Mesh2`를 `Mesh1` 아래에 붙입니다.
- `SetRelativeLocation`은 부모를 기준으로 한 위치입니다. `Mesh2`는 `Mesh1`보다 `50`만큼 아래에 놓입니다.

빌드하고 `MyActor`를 레벨에 끌어다 놓습니다. 디테일 패널 위쪽에 `Mesh1` 아래 `Mesh2`가 달린 계층이 보입니다. 각 컴포넌트를 선택하고 **스태틱 메시** 칸에서 모형을 하나씩 지정합니다.

`UPROPERTY(VisibleAnywhere)`를 지우고 빌드해 보면 디테일 패널에서 `Mesh2`를 선택했을 때 내용이 비어 보입니다. 리플렉션에 등록되지 않은 변수는 에디터가 알 수 없기 때문입니다. 확인한 뒤 다시 붙입니다.

### 15-5 객체 유형과 값 유형

`UPROPERTY`로 등록하는 변수는 두 종류로 나뉩니다.

| 종류 | 무엇인가 | 예 |
|---|---|---|
| 객체 유형 | 언리얼 객체를 가리키는 포인터 | `UStaticMeshComponent*` |
| 값 유형 | 숫자, 문자열, 구조체 같은 데이터 자체 | `int32`, `float`, `FString`, `FVector` |

값 유형 변수를 하나 추가해 지정자의 차이를 확인합니다.

```cpp
// MyActor.h
UPROPERTY(VisibleAnywhere)
int32 Level = 1;

UPROPERTY(EditAnywhere)
float Power = 10.0f;
```

빌드하고 디테일 패널을 보면 `Level`은 회색으로 표시되어 볼 수만 있고, `Power`는 숫자를 고칠 수 있습니다.

그런데 컴포넌트 포인터는 `VisibleAnywhere`였는데도 스태틱 메시를 바꿀 수 있었습니다. 포인터에 붙은 `VisibleAnywhere`는 "이 포인터가 어떤 컴포넌트를 가리키는지"를 못 바꾸게 할 뿐이고, 그 컴포넌트가 가진 프로퍼티는 컴포넌트의 것이라 따로 편집됩니다. 스태틱 메시를 고르는 칸은 액터의 변수가 아니라 `UStaticMeshComponent` 안에 있는 변수입니다.

컴포넌트 포인터에 `EditAnywhere`를 붙이면 가리키는 컴포넌트 자체를 다른 것으로 바꿀 수 있게 되어 버립니다. 의도하지 않은 일이므로 다음 규칙을 따릅니다.

| 변수 | 지정자 |
|---|---|
| 컴포넌트 포인터 | `VisibleAnywhere` |
| 에디터에서 조절할 값 | `EditAnywhere` |

### 15-6 생성자에서 에셋 지정

레벨에 놓을 때마다 스태틱 메시를 손으로 지정하지 않도록, Chapter 13에서 쓴 `FObjectFinder`로 기본 모형을 정합니다.

```cpp
// MyActor.cpp 생성자 끝에 추가
static ConstructorHelpers::FObjectFinder<UStaticMesh> SphereAsset(
	TEXT("/Game/StarterContent/Shapes/Shape_Sphere.Shape_Sphere"));

if (SphereAsset.Succeeded())
{
	Mesh2->SetStaticMesh(SphereAsset.Object);
}
```

빌드하면 콘텐츠 브라우저의 `MyActor` 아이콘에 구체가 보이고, 레벨에 놓자마자 `Mesh2`에 구체가 지정되어 있습니다.

`FObjectFinder`는 편리하지만 에셋 경로가 코드에 박힌다는 단점이 있습니다. 에셋을 다른 폴더로 옮기면 찾지 못합니다. 그래서 실제 프로젝트에서는 C++에는 포인터만 두고, 블루프린트 자식 클래스에서 에셋을 지정하는 방식을 더 많이 씁니다.

### 15-7 액터의 생애

액터는 만들어져서 사라질 때까지 정해진 순서로 함수가 호출됩니다.

| 시점 | 함수 | 하는 일 |
|---|---|---|
| 클래스 기본값 준비 | 생성자 | 컴포넌트 생성, 기본값 설정 |
| 컴포넌트 준비 완료 | `PostInitializeComponents` | 컴포넌트끼리 연결하는 초기화 |
| 게임 시작 | `BeginPlay` | 게임 시작 시 한 번 하는 일 |
| 매 프레임 | `Tick` | 계속 갱신할 일 |
| 사라질 때 | `EndPlay` | 정리 |

`Tick`은 매 프레임 호출되어 비용이 듭니다. 매 프레임 할 일이 없는 액터는 생성자에서 `PrimaryActorTick.bCanEverTick = false;`로 꺼 둡니다.

### 15-8 따라하기 — 눈사람 액터

1. `MyActor` 클래스를 만들고 헤더의 각 줄을 15-3의 설명과 대조해 읽는다.
2. `Mesh1`, `Mesh2`를 만들어 `Mesh1`을 루트로, `Mesh2`를 그 아래에 붙인다.
3. 두 메시에 모두 구체를 `FObjectFinder`로 지정한다.
4. `Mesh2`의 상대 위치를 `(0, 0, 80)`, 상대 스케일을 `(0.6, 0.6, 0.6)`으로 해서 눈사람 모양을 만든다. 스케일은 `SetRelativeScale3D` 함수로 정한다.
5. `float` 변수 `SpinSpeed`를 `EditAnywhere`로 추가하고 `Tick`에서 액터를 회전시킨다.
6. 레벨에 놓고 루트를 움직이면 `Mesh2`가 따라오는지 확인한다.

### 연습문제

1. 문제: 씬 컴포넌트와 액터 컴포넌트의 차이를 한 문장으로 쓰세요.  
   - 조건: 트랜스폼을 언급할 것
2. 문제: `MyActor.generated.h`를 포함할 때 지켜야 하는 위치 규칙을 쓰세요.
3. 문제: 컴포넌트 포인터에 `EditAnywhere` 대신 `VisibleAnywhere`를 쓰는 이유를 한 문장으로 쓰세요.

### 정답 포인트

- 씬 컴포넌트는 트랜스폼이 있어 계층에 붙고, 액터 컴포넌트는 트랜스폼 없이 기능만 준다. `#include` 중 맨 마지막. `EditAnywhere`면 가리키는 컴포넌트 자체를 바꿀 수 있게 되기 때문.

---

[상위 문서로 돌아가기](./README.md)
