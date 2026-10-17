# Chapter 14 리플렉션·이름 규칙·컨테이너·어서트

## 학습 목표

- 리플렉션 시스템이 왜 필요한지 설명하고 `UCLASS`·`UPROPERTY`·`UFUNCTION`을 구분해 쓸 수 있다.
- 언리얼의 클래스 접두사와 에셋 이름 규칙을 지켜 이름을 지을 수 있다.
- `TArray`·`TMap`·`TSet`의 쓰임을 구분해 쓸 수 있다.
- `check`와 `ensure` 어서트의 차이를 설명할 수 있다.

## 본문

### 14-1 리플렉션 시스템

Chapter 13에서 `FloatSpeed` 변수 위에 `UPROPERTY(EditAnywhere)`를 붙였더니 디테일 패널에 칸이 생겼습니다. 보통의 C++ 프로그램은 실행 중에 "이 클래스에 어떤 이름의 변수가 있는지"를 알 방법이 없습니다. 컴파일이 끝나면 변수 이름은 사라지고 메모리 주소만 남기 때문입니다.

에디터가 변수를 패널에 띄우고, 블루프린트가 C++ 함수를 노드로 보여 주려면 프로그램이 자기 자신의 클래스·변수·함수 정보를 실행 중에 알고 있어야 합니다. 이 기능을 **리플렉션**이라고 하고, 언리얼은 매크로로 표시한 것만 리플렉션에 등록합니다.

| 매크로 | 붙이는 곳 | 역할 |
|---|---|---|
| `UCLASS()` | 클래스 선언 위 | 이 클래스를 엔진에 등록한다 |
| `GENERATED_BODY()` | 클래스 안 첫 줄 | 엔진이 자동으로 만든 코드가 들어갈 자리 |
| `UPROPERTY()` | 멤버 변수 위 | 이 변수를 엔진에 등록한다 |
| `UFUNCTION()` | 멤버 함수 위 | 이 함수를 엔진에 등록한다 |

리플렉션에 등록되면 얻는 것은 다음과 같습니다.

- 디테일 패널에 변수가 표시된다.
- 블루프린트에서 변수와 함수를 쓸 수 있다.
- 블루프린트가 이 클래스를 부모로 삼을 수 있다.
- 엔진이 메모리를 대신 정리해 준다.

마지막 항목이 특히 중요합니다. C++에서 `new`로 만든 객체는 `delete`로 직접 지워야 하지만, 언리얼은 아무도 쓰지 않는 객체를 엔진이 찾아서 지웁니다. 이것을 **가비지 컬렉션**이라고 합니다. 엔진은 `UPROPERTY`가 붙은 포인터만 "누군가 쓰고 있다"고 인식합니다. 언리얼 객체를 가리키는 멤버 포인터에 `UPROPERTY`를 빼먹으면, 아직 쓰고 있는 객체가 지워져 게임이 멈출 수 있습니다.

### 14-2 자주 쓰는 지정자

매크로의 괄호 안에 적는 단어를 **지정자**라고 합니다. 지정자에 따라 변수와 함수가 에디터와 블루프린트에 어떻게 보일지가 정해집니다.

`UPROPERTY`의 지정자입니다.

| 지정자 | 의미 |
|---|---|
| `VisibleAnywhere` | 디테일 패널에서 볼 수만 있다 |
| `EditAnywhere` | 디테일 패널에서 수정할 수 있다 |
| `EditDefaultsOnly` | 블루프린트 에디터에서만 수정할 수 있고, 레벨의 인스턴스에서는 못 바꾼다 |
| `BlueprintReadOnly` | 블루프린트 그래프에서 값을 읽을 수만 있다 |
| `BlueprintReadWrite` | 블루프린트 그래프에서 값을 읽고 쓸 수 있다 |
| `Category = "이름"` | 디테일 패널과 노드 목록에서 묶일 분류 이름 |

`UFUNCTION`의 지정자입니다.

| 지정자 | 의미 |
|---|---|
| `BlueprintCallable` | 블루프린트에서 노드로 호출할 수 있다 |
| `BlueprintPure` | 값만 돌려주는 함수. 실행 핀 없는 노드가 된다 |

함수를 블루프린트에 노출하는 예입니다. `FloatingActor.h`의 클래스 안에 선언을, `.cpp`에 구현을 넣습니다.

```cpp
// FloatingActor.h
UFUNCTION(BlueprintCallable, Category = "FloatingActor")
void SetSpeeds(float NewFloatSpeed, float NewRotationSpeed);
```

```cpp
// FloatingActor.cpp
void AFloatingActor::SetSpeeds(float NewFloatSpeed, float NewRotationSpeed)
{
	FloatSpeed = NewFloatSpeed;
	RotationSpeed = NewRotationSpeed;
}
```

빌드한 뒤 `BP_FloatingActor`의 이벤트 그래프에서 우클릭해 `Set Speeds`를 검색하면 노드가 나옵니다. C++로 만든 기능을 블루프린트에서 불러 쓰는 가장 기본적인 방법입니다.

### 14-3 클래스 이름 접두사

언리얼 C++에서는 타입 이름 맨 앞에 종류를 나타내는 글자를 붙입니다. 이 글자를 **접두사**라고 합니다. 규칙을 지키지 않으면 빌드 오류가 납니다.

| 접두사 | 붙이는 대상 | 예 |
|---|---|---|
| `A` | 액터를 상속받은 클래스 | `AActor`, `AFloatingActor` |
| `U` | 액터가 아닌 언리얼 객체 클래스 | `UStaticMeshComponent` |
| `F` | 구조체와 일반 C++ 클래스 | `FVector`, `FRotator` |
| `E` | 열거형 | `ECollisionChannel` |
| `I` | 인터페이스 | `IOnlineSession` |
| `T` | 템플릿 | `TArray` |

변수 이름에도 규칙이 있습니다. 참·거짓을 담는 `bool` 변수는 `bCanEverTick`처럼 소문자 `b`로 시작합니다. 그 외의 변수와 함수는 `FloatSpeed`처럼 단어마다 첫 글자를 대문자로 씁니다.

엔진이 자주 쓰는 기본 타입도 이름이 따로 있습니다.

| 타입 | 담는 값 |
|---|---|
| `int32` | 정수 |
| `float` | 실수 |
| `bool` | 참·거짓 |
| `FString` | 문자열. 자유롭게 고칠 수 있다 |
| `FName` | 이름표용 문자열. 비교가 빠르다 |

### 14-4 에셋 이름 규칙

콘텐츠 브라우저의 파일 이름에도 널리 쓰이는 규칙이 있습니다. 이름 앞에 에셋 종류를 나타내는 글자를 붙입니다. 지금까지 `BP_`, `M_`, `SM_`으로 시작하는 이름을 써 온 이유입니다.

| 접두사 | 에셋 종류 |
|---|---|
| `BP_` | 블루프린트 클래스 |
| `SM_` | 스태틱 메시 |
| `SK_` | 스켈레탈 메시 |
| `M_` | 머티리얼 |
| `MI_` | 머티리얼 인스턴스 |
| `MF_` | 머티리얼 함수 |
| `T_` | 텍스처 |
| `ABP_` | 애니메이션 블루프린트 |
| `WBP_` | 위젯 블루프린트 |

엔진이 강제하는 규칙은 아니지만, 이름만 보고 종류를 알 수 있고 검색하기 쉬워서 대부분의 팀이 따릅니다. 팀 프로젝트를 시작할 때 폴더 구조와 함께 가장 먼저 정하는 약속입니다.

### 14-5 언리얼 컨테이너

여러 개의 값을 모아 담는 타입을 **컨테이너**라고 합니다. C++ 표준 라이브러리에 `vector`, `map`, `set`이 있지만 언리얼 코드에서는 엔진이 제공하는 컨테이너를 씁니다. 리플렉션과 가비지 컬렉션에 맞게 만들어져 있어 `UPROPERTY`와 함께 쓸 수 있기 때문입니다.

| 타입 | 역할 | 예 |
|---|---|---|
| `TArray` | 순서가 있는 배열. 크기가 늘어난다 | 적 목록, 순찰 지점 |
| `TMap` | 키로 값을 찾는다 | 무기 이름으로 탄약 수 찾기 |
| `TSet` | 중복 없는 집합 | 이미 맞힌 액터 기록 |

`FloatingActor`의 `BeginPlay` 안에 아래 코드를 넣어 사용법을 확인해 봅니다.

```cpp
TArray<int32> Scores;
Scores.Add(10);
Scores.Add(30);
Scores.Add(20);

int32 Total = 0;
for (int32 Score : Scores)
{
	Total += Score;
}
// Total은 60, Scores.Num()은 3

TMap<FName, int32> AmmoByWeapon;
AmmoByWeapon.Add(TEXT("Rifle"), 30);
AmmoByWeapon.Add(TEXT("Pistol"), 12);

if (AmmoByWeapon.Contains(TEXT("Rifle")))
{
	int32 RifleAmmo = AmmoByWeapon[TEXT("Rifle")];
	// RifleAmmo는 30
}

TSet<FName> Visited;
Visited.Add(TEXT("Gate"));
Visited.Add(TEXT("Gate"));
// 같은 값을 두 번 넣어도 Visited.Num()은 1
```

**코드 해석**

- `Add`는 세 컨테이너 모두에서 값을 넣는 함수입니다. `TMap`은 키와 값을 함께 넣습니다.
- `Num`은 들어 있는 개수를 돌려줍니다.
- `for (타입 이름 : 컨테이너)`는 처음부터 끝까지 하나씩 꺼내는 반복문입니다.
- `Contains`는 그 값 또는 키가 들어 있는지 확인합니다.
- `TMap`에서 `[키]`는 그 키에 해당하는 값을 꺼냅니다. 없는 키를 꺼내면 오류가 나므로 `Contains`로 먼저 확인합니다.

결과 값을 화면에서 확인하는 방법은 Chapter 16의 로그에서 배웁니다. 지금은 Visual Studio의 중단점으로 변수 값을 확인하거나 코드만 읽고 넘어가도 됩니다.

### 14-6 어서트

코드를 짜다 보면 "여기서 이 포인터는 절대 비어 있을 리 없다"고 가정하는 곳이 생깁니다. 가정이 깨졌는데 프로그램이 조용히 계속 실행되면, 한참 뒤 엉뚱한 곳에서 문제가 터져 원인을 찾기 어렵습니다.

**어서트**는 가정이 깨진 순간 바로 알려 주는 검사 매크로입니다. `if` 문과 로그를 길게 적는 대신 한 줄로 씁니다.

| 매크로 | 조건이 거짓일 때 |
|---|---|
| `check(조건)` | 프로그램을 즉시 멈춘다 |
| `ensure(조건)` | 오류 기록을 남기고 계속 실행한다 |
| `ensureMsgf(조건, TEXT("설명"))` | 설명 글과 함께 기록을 남기고 계속 실행한다 |

```cpp
check(VisualMesh != nullptr);
ensureMsgf(FloatSpeed > 0.0f, TEXT("FloatSpeed should be positive"));
```

**코드 해석**

- 첫 줄은 `VisualMesh`가 비어 있으면 더 진행할 수 없으므로 `check`로 멈춥니다.
- 둘째 줄은 `FloatSpeed`가 `0` 이하여도 게임이 망가지지는 않으므로 `ensureMsgf`로 기록만 남깁니다.

`ensure`는 조건의 참·거짓을 돌려주므로 `if (ensure(조건))`처럼 조건문 안에 넣어 쓸 수도 있습니다.

어서트는 개발 중에 실수를 빨리 찾기 위한 도구입니다. 배포용 빌드에서는 `check`가 제거될 수 있으므로, `check` 괄호 안에 꼭 실행되어야 하는 코드를 넣으면 안 됩니다. 플레이 중에 일어날 수 있는 정상적인 상황은 어서트가 아니라 `if` 문으로 처리합니다. 액터 포인터가 아직 유효한지 확인할 때는 `IsValid(포인터)` 함수를 씁니다.

### 14-7 따라하기 — 코드 분석과 이름 정리

1. `FloatingActor.h`를 열어 `UCLASS`, `GENERATED_BODY`, `UPROPERTY`가 어디에 붙어 있는지 표시해 본다.
2. `FloatSpeed`의 지정자를 `EditAnywhere`에서 `VisibleAnywhere`로 바꿔 빌드하고 디테일 패널에서 차이를 확인한다. 확인 후 되돌린다.
3. `SetSpeeds` 함수를 `BlueprintCallable`로 추가하고 `BP_FloatingActor`의 BeginPlay에서 호출한다.
4. `BeginPlay`에 `TArray`, `TMap`, `TSet` 예제 코드를 넣고 빌드가 되는지 확인한다.
5. 생성자 끝에 `check(VisualMesh != nullptr);`을 넣는다.
6. 콘텐츠 브라우저에서 지금까지 만든 에셋의 이름이 14-4의 규칙에 맞는지 확인하고 고친다.

### 연습문제

1. 문제: 언리얼 객체를 가리키는 멤버 포인터에 `UPROPERTY`를 붙이지 않으면 생길 수 있는 문제를 한 문장으로 쓰세요.  
   - 조건: 가비지 컬렉션을 언급할 것
2. 문제: 다음 이름에 붙을 접두사를 쓰세요. 액터를 상속받은 `Enemy` 클래스, 구조체 `ItemData`, 참·거짓 변수 `IsDead`.
3. 문제: 무기 이름으로 남은 탄약 수를 바로 찾으려면 어떤 컨테이너가 맞는지 쓰고 이유를 한 문장으로 쓰세요.

### 정답 포인트

- 엔진이 쓰고 있는 줄 모르고 가비지 컬렉션으로 지울 수 있다. `AEnemy`, `FItemData`, `bIsDead`. 키로 값을 찾는 `TMap`.

---

[상위 문서로 돌아가기](./README.md)
