# Chapter 35 캐릭터·이동 리플리케이션

## 학습 목표

- 캐릭터 무브먼트가 이동을 예측, 보정, 보간으로 동기화하는 방식을 설명할 수 있다.
- 클라이언트에서만 이동 값을 바꿨을 때 생기는 덜컹거림을 재현하고 원인을 설명할 수 있다.
- 달리기 상태를 Server RPC와 RepNotify로 동기화할 수 있다.
- 대시 같은 특수 이동을 서버에서 처리하게 만들 수 있다.

## 본문

### 35-1 이동은 이미 복제되고 있다

Chapter 30에서 템플릿 캐릭터의 이동은 처음부터 두 창에 모두 보였습니다. 우리가 코드를 쓰지 않았는데도 동기화되는 이유는 **캐릭터 무브먼트** 컴포넌트가 그 일을 하고 있기 때문입니다.

이동을 Chapter 33의 발사처럼 처리한다고 생각해 봅니다. 키를 누르면 서버에 요청하고, 서버가 움직인 결과를 받은 뒤에야 내 화면의 캐릭터가 움직입니다. 요청이 가고 답이 오는 시간만큼 조작이 늦게 반응해서 게임을 할 수 없습니다.

캐릭터 무브먼트는 이 문제를 세 가지 방법으로 풉니다.

| 방법 | 누가 | 하는 일 |
|---|---|---|
| 예측 | 내 클라이언트 | 서버의 답을 기다리지 않고 먼저 움직인다. 움직인 내용을 서버에 보낸다 |
| 보정 | 서버 | 같은 입력으로 직접 움직여 보고, 클라이언트의 결과와 다르면 올바른 위치를 돌려보낸다 |
| 보간 | 다른 클라이언트 | 띄엄띄엄 도착하는 위치 사이를 부드럽게 이어 그린다 |

Chapter 34의 롤로 말하면 오토노머스 프록시는 예측하고, 오소리티는 확인하고 보정하며, 시뮬레이티드 프록시는 보간합니다. 평소에는 클라이언트와 서버가 같은 입력과 같은 설정으로 계산하므로 결과가 같고, 보정은 일어나지 않습니다.

### 35-2 클라이언트에서만 속도를 바꾸면

왼쪽 `Shift`를 누르는 동안 빨리 달리는 기능을 넣어 봅니다. 먼저 일부러 잘못된 방법으로 만듭니다.

1. 입력 액션 `IA_Sprint`를 만들고 `IMC_Default`에서 **왼쪽 Shift**에 연결한다.
2. 캐릭터에 추가한다.

```cpp
// MyCharacter.h
private:
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, meta = (AllowPrivateAccess = "true"))
	UInputAction* SprintAction;

	void StartSprint();
	void StopSprint();

	UPROPERTY(EditAnywhere, Category = Movement)
	float WalkSpeed = 500.0f;

	UPROPERTY(EditAnywhere, Category = Movement)
	float SprintSpeed = 900.0f;
```

```cpp
// MyCharacter.cpp — SetupPlayerInputComponent 안
EnhancedInputComponent->BindAction(SprintAction, ETriggerEvent::Started, this, &AMyCharacter::StartSprint);
EnhancedInputComponent->BindAction(SprintAction, ETriggerEvent::Completed, this, &AMyCharacter::StopSprint);
```

```cpp
// MyCharacter.cpp
void AMyCharacter::StartSprint()
{
	GetCharacterMovement()->MaxWalkSpeed = SprintSpeed;
}

void AMyCharacter::StopSprint()
{
	GetCharacterMovement()->MaxWalkSpeed = WalkSpeed;
}
```

3. 빌드하고 캐릭터 블루프린트의 **Sprint Action**에 `IA_Sprint`를 지정한다.
4. 플레이어 수 2, 리슨 서버로 실행한다.

호스트 창에서는 잘 달립니다. 클라이언트 창에서 `Shift`를 누르고 달리면 캐릭터가 앞으로 나갔다가 뒤로 끌려오기를 반복하며 덜컹거립니다.

클라이언트는 최대 속도 `900`으로 예측해 앞서 나갑니다. 서버에 있는 같은 캐릭터의 최대 속도는 여전히 `500`입니다. 서버가 같은 입력으로 계산한 위치는 클라이언트보다 뒤에 있으므로, 서버는 계속 "네 위치는 틀렸다"며 보정을 보냅니다. 이동에 영향을 주는 값이 클라이언트와 서버에서 다르면 이런 일이 생깁니다.

### 35-3 달리기 상태를 서버에 알리기

서버도 같은 속도를 쓰게 만듭니다. 달리는 중인지를 서버에 알리고, 서버가 그 상태를 모두에게 복제합니다.

```cpp
// MyCharacter.h
private:
	UFUNCTION(Server, Reliable)
	void Server_SetSprinting(bool bNewSprinting);

	UFUNCTION()
	void OnRep_IsSprinting();

	void ApplySprintSpeed();

	UPROPERTY(ReplicatedUsing = OnRep_IsSprinting)
	bool bIsSprinting = false;
```

```cpp
// MyCharacter.cpp
void AMyCharacter::StartSprint()
{
	bIsSprinting = true;
	ApplySprintSpeed();
	Server_SetSprinting(true);
}

void AMyCharacter::StopSprint()
{
	bIsSprinting = false;
	ApplySprintSpeed();
	Server_SetSprinting(false);
}

void AMyCharacter::Server_SetSprinting_Implementation(bool bNewSprinting)
{
	bIsSprinting = bNewSprinting;
	ApplySprintSpeed();
}

void AMyCharacter::OnRep_IsSprinting()
{
	ApplySprintSpeed();
}

void AMyCharacter::ApplySprintSpeed()
{
	GetCharacterMovement()->MaxWalkSpeed = bIsSprinting ? SprintSpeed : WalkSpeed;
}
```

Chapter 30에서 만든 `GetLifetimeReplicatedProps`에 한 줄을 추가합니다.

```cpp
DOREPLIFETIME(AMyCharacter, bIsSprinting);
```

**코드 해석**

- `StartSprint`는 입력을 받은 컴퓨터에서 먼저 속도를 바꾸고, 바로 서버에 알립니다. 서버의 답을 기다리지 않으므로 누르는 즉시 빨라집니다.
- `Server_SetSprinting_Implementation`은 서버의 같은 캐릭터에 같은 속도를 적용합니다. 이제 클라이언트의 예측과 서버의 계산이 같은 값을 씁니다.
- `bIsSprinting`은 RepNotify로 복제됩니다. 다른 클라이언트의 화면에 있는 이 캐릭터도 `OnRep_IsSprinting`에서 같은 속도가 됩니다.
- `조건 ? 값1 : 값2`는 조건이 참이면 앞의 값을, 거짓이면 뒤의 값을 고르는 C++의 삼항 연산자입니다.

클라이언트 창에서 다시 달려 봅니다. 덜컹거림이 사라지고, 호스트 창에서도 그 캐릭터가 빨라진 것이 보입니다. Server RPC가 도착하기 전의 아주 짧은 순간에는 두 컴퓨터의 속도가 다르지만, 그 차이는 눈에 띄지 않는 작은 보정으로 지나갑니다.

`bIsSprinting`을 `BlueprintReadOnly`로 바꾸면 Chapter 18의 애니메이션 블루프린트에서 읽어 달리기 동작을 따로 재생할 수 있습니다. 상태가 복제되므로 다른 사람 화면에서도 같은 동작이 나옵니다.

### 35-4 위치를 직접 바꾸면 안 되는 이유

Chapter 13의 부유하는 큐브처럼 `Tick`에서 `SetActorLocation`으로 캐릭터를 직접 옮기면 캐릭터 무브먼트의 예측과 보정을 거치지 않습니다.

| 어디서 호출했는가 | 결과 |
|---|---|
| 클라이언트에서만 | 서버는 캐릭터가 움직인 줄 모른다. 다음 보정 때 원래 자리로 끌려온다 |
| 서버에서만 | 위치는 복제되지만 그 캐릭터의 주인 화면에서는 갑자기 튄다 |

네트워크에서 캐릭터를 움직일 때는 `AddMovementInput`, `Jump`, `LaunchCharacter`처럼 캐릭터 무브먼트가 제공하는 길을 씁니다.

### 35-5 대시는 서버에서

앞으로 순간적으로 튀어 나가는 대시를 만듭니다. 위치를 크게 바꾸는 동작이므로 서버가 실행해야 합니다.

1. 입력 액션 `IA_Dash`를 만들고 `IMC_Default`에서 원하는 키에 연결한다.
2. 캐릭터에 추가한다.

```cpp
// MyCharacter.h
private:
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, meta = (AllowPrivateAccess = "true"))
	UInputAction* DashAction;

	void Dash();

	UFUNCTION(Server, Reliable)
	void Server_Dash();

	UPROPERTY(EditAnywhere, Category = Movement)
	float DashPower = 1500.0f;
```

```cpp
// MyCharacter.cpp — SetupPlayerInputComponent 안
EnhancedInputComponent->BindAction(DashAction, ETriggerEvent::Started, this, &AMyCharacter::Dash);
```

```cpp
// MyCharacter.cpp
void AMyCharacter::Dash()
{
	Server_Dash();
}

void AMyCharacter::Server_Dash_Implementation()
{
	LaunchCharacter(GetActorForwardVector() * DashPower, true, false);
}
```

**코드 해석**

- `LaunchCharacter(속도, 수평 속도 덮어쓰기, 수직 속도 덮어쓰기)`는 캐릭터에 순간적인 속도를 줍니다. 캐릭터 무브먼트를 거치므로 벽에 막히고 바닥을 따라갑니다.
- `GetActorForwardVector()`는 캐릭터가 바라보는 방향의 길이 1 벡터입니다.
- 서버에서 실행하므로 대시한 위치가 모든 컴퓨터에 전달됩니다.

클라이언트 창에서 대시하면 내 캐릭터가 한 박자 늦게 튀어 나갑니다. 요청이 서버에 갔다가 보정으로 돌아오는 시간입니다. 누르는 순간 반응하게 하려면 대시도 달리기처럼 클라이언트가 먼저 예측해야 하는데, 속도가 순간적으로 바뀌는 동작의 예측은 캐릭터 무브먼트 컴포넌트를 상속받아 확장해야 해서 이 과정의 범위를 넘습니다. 지금은 **결과를 바꾸는 이동은 서버가 실행한다**는 원칙만 지킵니다.

대시를 연타하지 못하게 하려면 Chapter 33의 `CanFire`와 같은 방식으로 서버에서 간격을 검사합니다.

### 35-6 이동이 어긋날 때 확인 순서

1. 한 창에서만 움직인다면, 위치를 `SetActorLocation`으로 직접 바꾸는 코드가 있는지 찾는다.
2. 내 캐릭터가 덜컹거린다면, `MaxWalkSpeed`처럼 이동에 쓰이는 값을 한쪽 컴퓨터에서만 바꾸고 있는지 찾는다.
3. 다른 사람 캐릭터가 순간이동하듯 보인다면, 캐릭터 블루프린트 클래스 디폴트의 **무브먼트 리플리케이트**가 켜져 있는지 확인한다.
4. 입력이 남의 캐릭터에 들어간다면, 입력을 받는 코드가 `SetupPlayerInputComponent`의 바인딩을 거치는지 확인한다.
5. 장애물에 걸려 못 가는 것이라면 네트워크 문제가 아니다. 플레이어 수를 1로 줄여 같은 증상이 나는지 먼저 본다.

### 35-7 따라하기 — 달리기와 대시

1. 플레이어 수 2, 리슨 서버로 실행해 두 창에서 서로의 걷기와 점프가 보이는지 확인한다.
2. 35-2의 잘못된 달리기를 넣고 클라이언트 창에서 덜컹거림을 직접 본다.
3. 35-3처럼 고쳐 덜컹거림이 사라지는지 확인한다.
4. 호스트 창에서 클라이언트의 캐릭터가 달릴 때 실제로 빨라지는지 확인한다.
5. 대시를 넣고 두 창에서 같은 위치로 이동하는지 확인한다.
6. 대시에 1초 간격 제한을 서버 쪽에 넣는다.
7. `Server_Dash_Implementation` 안의 `LaunchCharacter`를 `Dash`로 옮겨 클라이언트에서만 실행되게 바꿔 보고, 어떤 일이 생기는지 확인한 뒤 되돌린다.

### 연습문제

1. 문제: 내 캐릭터의 이동을 서버의 답을 기다리지 않고 먼저 움직이게 하는 이유를 한 문장으로 쓰세요.
2. 문제: 클라이언트에서만 `MaxWalkSpeed`를 올렸을 때 캐릭터가 덜컹거리는 이유를 두 문장으로 쓰세요.  
   - 조건: 예측과 보정을 언급할 것
3. 문제: 달리기 상태를 Server RPC로만 보내고 복제하지 않으면 다른 클라이언트 화면에서 무엇이 빠지는지 한 문장으로 쓰세요.
4. 문제: 대시를 클라이언트에서만 실행하면 생기는 일을 한 문장으로 쓰세요.

### 정답 포인트

- 요청이 오가는 시간만큼 조작이 늦게 반응하기 때문이다. 클라이언트는 빠른 속도로 예측해 앞서 나가는데, 서버는 원래 속도로 계산해 뒤쪽 위치로 보정을 보낸다. 다른 클라이언트에 있는 그 캐릭터는 달리는 중인지 알 수 없어 달리기 동작을 재생하지 못한다. 서버가 모르는 이동이라 곧 보정되어 원래 자리로 끌려온다.

---

[상위 문서로 돌아가기](./README.md)
