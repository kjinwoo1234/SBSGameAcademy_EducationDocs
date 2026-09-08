# Chapter 01 에디터·언리얼 C++

## 학습 목표

- C++ 클래스로 액터를 만들고 `UCLASS` / `UPROPERTY` / `UFUNCTION`을 구분해 쓸 수 있다.
- 블루프린트에서 C++ 클래스를 상속·배치해 에디터와 코드를 연결할 수 있다.
- Tick으로 오브젝트를 움직이는 최소 루프를 작성할 수 있다.

## 본문

### 01-1 프로젝트와 모듈

**블루프린트(Blueprint)**는 언리얼 에디터에서 **노드(상자)를 선으로 연결해** 동작을 만드는 시각적 스크립트입니다. C++처럼 글자로 코드를 쓰기보다, 그래프 화면에서 “이벤트 → 동작”을 이어 가는 방식에 가깝습니다.

![블루프린트 이벤트 그래프: 이벤트 노드와 함수 노드가 선으로 연결된 예시 화면](./img/01-blueprint-event-graph-example.png)

프로젝트를 만들 때 **C++ 프로젝트**를 고르면 처음부터 코드용 모듈이 열리고, **블루프린트 프로젝트**로 시작해도 나중에 C++ 클래스를 추가할 수 있습니다. 이 장은 C++로 타입을 만들고, 필요하면 블루프린트로 **겉모습·수치**를 다듬는 흐름을 목표로 합니다.

**액터(Actor)**는 레벨(맵)에 배치할 수 있는 오브젝트의 기본 단위입니다. 떠 있는 큐브, 캐릭터, 라이트처럼 “세상에 놓이는 것”을 액터로 생각하면 됩니다. C++에서는 보통 `AActor`를 상속해 새 액터 클래스를 만듭니다.

언리얼에서 C++ 작업을 하려면 **C++ 프로젝트**로 생성하거나, 블루프린트 프로젝트에 C++ 클래스를 추가합니다. 생성 후 IDE에서 솔루션을 열면 **게임 모듈** 아래 `.h` / `.cpp` 쌍이 보입니다.

**모듈**은 빌드 단위입니다. 새 클래스를 넣으면 보통 게임 모듈의 `Build.cs`에 필요한 모듈 의존성이 이미 잡혀 있습니다. 이 장에서는 새 모듈을 나누지 않고 게임 모듈 안에 액터 하나를 만듭니다.

에디터 **툴바 → Compile** 또는 IDE 빌드로 코드를 반영합니다. 컴파일이 끝나야 새 타입이 콘텐츠 브라우저·블루프린트에 나타납니다.

![언리얼 에디터 툴바의 Compile 버튼과 콘텐츠 브라우저에 새 C++ 클래스가 보이는 위치](./img/01-editor-toolbar-compile.png)

### 01-2 `UCLASS` · `UPROPERTY` · `UFUNCTION`

언리얼 C++의 핵심은 **리플렉션**입니다. 엔진이 타입·프로퍼티·함수 이름을 런타임에 알 수 있게, 매크로로 표시합니다.

| 매크로 | 역할 |
|---|---|
| `UCLASS()` | 이 클래스를 언리얼 타입으로 등록. 블루프린트에서 쓰려면 `Blueprintable` 등 지정 |
| `UPROPERTY()` | 멤버를 엔진이 관리. 에디터 노출·**가비지 컬렉션**·복제에 관여 |
| `UFUNCTION()` | 함수를 블루프린트·리플렉션에 노출 |

**가비지 컬렉션(garbage collection)**은 “더 이상 안 쓰는 객체를 엔진이 알아서 치우는” 메모리 정리입니다. `UPROPERTY`로 표시된 언리얼 객체 포인터는 이 정리 대상에 포함되기 쉽습니다. 세부는 뒤 장에서 다루고, 지금은 “엔진이 관리하려면 `UPROPERTY`가 필요하다” 정도만 기억하면 됩니다.

헤더에서 클래스를 선언할 때 `GENERATED_BODY()`를 클래스 안에 둡니다. 이 줄은 언리얼 빌드 도구가 채우는 코드의 자리입니다. 지금은 형태만 맞추고, 매 줄의 생성 원리는 깊게 외우지 않아도 됩니다.

아래는 떠 있는 큐브용 액터 골격입니다. 프로젝트에 **New C++ Class → Actor**로 `AFloatingActor`를 만든 뒤, 헤더·소스를 이 구조에 맞게 채웁니다.

```cpp
// FloatingActor.h
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "FloatingActor.generated.h"

UCLASS()
class YOURPROJECT_API AFloatingActor : public AActor
{
	GENERATED_BODY()

public:
	AFloatingActor();

protected:
	virtual void BeginPlay() override;

public:
	virtual void Tick(float DeltaTime) override;

	UPROPERTY(VisibleAnywhere)
	UStaticMeshComponent* Mesh;

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Float")
	float FloatSpeed;

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Float")
	float FloatAmplitude;
};
```

```cpp
// FloatingActor.cpp
#include "FloatingActor.h"

AFloatingActor::AFloatingActor()
{
	PrimaryActorTick.bCanEverTick = true;

	Mesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"));
	RootComponent = Mesh;

	FloatSpeed = 2.0f;
	FloatAmplitude = 20.0f;
}

void AFloatingActor::BeginPlay()
{
	Super::BeginPlay();
}

void AFloatingActor::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);

	const FVector Loc = GetActorLocation();
	const float Z = FMath::Sin(GetWorld()->GetTimeSeconds() * FloatSpeed) * FloatAmplitude;
	SetActorLocation(FVector(Loc.X, Loc.Y, Loc.Z + Z * DeltaTime));
}
```

**코드 해석**

- `CreateDefaultSubobject`로 메시 컴포넌트를 만들고 `RootComponent`로 둡니다.
- `EditAnywhere`면 에디터 디테일 패널에서 `FloatSpeed` 등을 바꿀 수 있습니다.
- `Tick`에서 시간에 따른 `Sin`으로 위아래 이동을 줍니다. 값은 예시이므로 느낌에 맞게 조절합니다.

`YOURPROJECT_API`와 모듈 매크로 이름은 프로젝트마다 다릅니다. 새로 만든 클래스 헤더에 이미 있는 이름을 그대로 쓰세요.

### 01-3 블루프린트 연계

C++ 클래스만으로도 레벨에 배치할 수 있지만, 메시·재질을 에디터에서 바꾸려면 **블루프린트 자식**을 만듭니다.

1. 콘텐츠 브라우저에서 우클릭 → **Blueprint Class**
2. 부모로 `FloatingActor` 선택

![콘텐츠 브라우저에서 Blueprint Class를 고르고 부모로 FloatingActor를 선택하는 픽커 화면](./img/01-blueprint-parent-class-picker.png)

3. 열린 블루프린트에서 `Mesh`에 Static Mesh를 지정

![블루프린트 뷰포트·디테일에서 Static Mesh 컴포넌트에 큐브 메시를 지정한 화면](./img/01-floating-bp-mesh-details.png)

4. 레벨에 블루프린트 액터를 배치하고 플레이

![레벨에 부유 액터 블루프린트를 배치하고 디테일에서 FloatSpeed를 조절하는 화면](./img/01-floating-actor-in-level.png)

C++에서 `FloatSpeed`를 바꾸면 부모 기본값이 바뀝니다. 인스턴스마다 다르게 쓰려면 블루프린트나 레벨에 배치된 액터의 디테일에서 덮어씁니다.

`UFUNCTION(BlueprintCallable)`을 붙인 함수는 블루프린트 그래프에서 노드로 호출할 수 있습니다. 이 장에서는 Tick 이동만으로도 충분합니다. 입력·전투는 다음 장에서 다룹니다.

### 01-4 리플렉션이 필요한 이유

에디터 디테일, 블루프린트 노드, 세이브·네트워크 복제는 모두 **이름과 타입을 엔진이 알아야** 동작합니다. 일반 C++ 멤버만 두면 컴파일러는 알지만 에디터는 모릅니다. 그래서 `UCLASS` / `UPROPERTY` / `UFUNCTION`으로 **노출 범위를 명시**합니다.

이 장 범위는 프로젝트에 C++ 액터를 넣고, 프로퍼티를 에디터에 노출하며, Tick으로 움직이는 것까지입니다. 입력 시스템과 캐릭터 이동은 Chapter 02에서 이어갑니다.

### 01-5 따라하기 — 부유 오브젝트·리플렉션 클래스

1. New C++ Class → Actor로 `AFloatingActor`를 만들고 위 골격을 채운다.
2. Compile 후 블루프린트 자식을 만들어 메시를 지정한다.
3. 레벨에 배치하고 `FloatSpeed`·진폭을 디테일에서 바꿔 커스터마이징한다.
4. `UPROPERTY`로 `FloatAmplitude` 같은 값을 하나 더 노출해 에디터에서 조절한다.
5. 선택: `UFUNCTION(BlueprintCallable)`로 속도를 바꾸는 함수를 하나 두고 블루프린트에서 호출해 본다.

### 연습문제

1. 문제: `AFloatingActor`에 `FloatSpeed`를 에디터에서 바꿀 수 있게 하는 `UPROPERTY` 지정자 조합을 쓰세요.  
   - 조건: `EditAnywhere`를 포함할 것
2. 문제: 블루프린트 자식을 만드는 이유를 두 문장 이내로 쓰세요.  
   - 조건: 메시 또는 재질 중 하나를 언급할 것
3. 문제: `PrimaryActorTick.bCanEverTick = true`를 빼면 무엇이 멈추는지 한 줄로 쓰세요.

### 정답 포인트

- 에디터 수정 → `EditAnywhere` 계열. 블루프린트 자식 → 에셋·수치를 에디터에서 다루기 쉽게. Tick 비활성 → `Tick` 미호출.

---

[상위 문서로 돌아가기](./README.md)
