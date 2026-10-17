# Chapter 33 전투 리플리케이션

## 학습 목표

- 발사 요청을 Server RPC로 보내는 이유를 설명할 수 있다.
- 발사체를 서버에서 스폰하고 모든 창에 보이게 복제할 수 있다.
- 판정과 데미지는 서버에, 소리와 이펙트는 Multicast RPC에 나누어 둘 수 있다.
- 근접 콤보 공격을 여러 컴퓨터에서 같은 동작으로 보이게 만들 수 있다.

## 본문

### 33-1 지금 무엇이 어긋나는가

Chapter 32까지 고친 게임을 플레이어 수 2, 리슨 서버로 실행하고 **클라이언트 창**에서 공격해 봅니다. 세 가지가 어긋납니다.

| 행동 | 클라이언트 창 | 호스트 창 |
|---|---|---|
| 히트스캔 발사 | 디버그 선이 보이지만 상대 체력이 줄지 않는다 | 아무 일도 없다 |
| 발사체 발사 | 구체가 날아간다 | 구체가 보이지 않는다 |
| 근접 공격 | 내 캐릭터가 검을 휘두른다 | 그 캐릭터가 가만히 서 있다 |

원인은 모두 같습니다. `Fire`와 `Attack`이 입력을 받은 컴퓨터에서만 실행되기 때문입니다. 클라이언트가 `ApplyDamage`를 호출해도 Chapter 32의 가드가 막으므로 체력은 줄지 않습니다. 가드는 제 역할을 한 것이고, 빠진 것은 "서버에게 대신 해 달라고 요청하는 길"입니다.

호스트 창에서 공격하면 데미지가 들어갑니다. 호스트는 서버이기도 하기 때문입니다. 한쪽 창에서만 되는 기능은 대부분 이 차이에서 나옵니다. 멀티플레이 기능은 항상 **클라이언트 창에서** 확인합니다.

### 33-2 발사 요청을 Server RPC로 보내기

Chapter 30에서 선언만 해 본 `Server_RequestFire`를 실제로 구현합니다. 클라이언트는 "쏘고 싶다"는 요청만 보내고, 쏠 수 있는지 판단하고 쏘는 일은 서버가 합니다.

먼저 Chapter 27의 `Fire` 안에 있던 내용을 함수 두 개로 옮깁니다. 27-7의 6번을 이미 했다면 그 함수를 그대로 씁니다.

```cpp
// MyCharacter.h
private:
	UFUNCTION(Server, Reliable)
	void Server_RequestFire();

	bool CanFire() const;
	void FireHitscan();
	void FireProjectile();

	UPROPERTY(EditAnywhere, Category = Attack)
	bool bUseProjectile = false;

	UPROPERTY(EditAnywhere, Category = Attack)
	float FireInterval = 0.2f;

	float LastFireTime = -100.0f;
```

```cpp
// MyCharacter.cpp
void AMyCharacter::Fire()
{
	Server_RequestFire();
}

bool AMyCharacter::CanFire() const
{
	return GetWorld()->GetTimeSeconds() - LastFireTime >= FireInterval;
}

void AMyCharacter::Server_RequestFire_Implementation()
{
	if (!CanFire())
	{
		return;
	}

	LastFireTime = GetWorld()->GetTimeSeconds();

	if (bUseProjectile)
	{
		FireProjectile();
	}
	else
	{
		FireHitscan();
	}
}
```

`FireHitscan`에는 27-4의 라인 트레이스 코드를, `FireProjectile`에는 27-6의 스폰 코드를 그대로 옮깁니다.

**코드 해석**

- `Fire`는 입력을 받은 컴퓨터에서 실행됩니다. 이제 직접 쏘지 않고 `Server_RequestFire()`를 호출하기만 합니다.
- `Server_RequestFire_Implementation`은 서버에서 실행됩니다. Chapter 30에서 설명한 대로 호출하는 이름과 내용을 적는 함수의 이름이 다릅니다. 헤더에는 `Server_RequestFire`만 선언하고, `_Implementation`은 선언하지 않습니다.
- `CanFire`는 마지막 발사 뒤 `FireInterval`초가 지났는지 확인합니다. `GetWorld()->GetTimeSeconds()`는 레벨이 시작된 뒤 흐른 시간입니다.
- `LastFireTime`은 서버에서만 바뀝니다. 클라이언트가 조작된 프로그램으로 요청을 초당 수백 번 보내도 서버가 간격을 지킵니다.
- 호스트가 `Server_RequestFire()`를 호출하면 네트워크를 거치지 않고 그 자리에서 `_Implementation`이 실행됩니다. 호스트와 클라이언트가 같은 코드 경로를 쓰게 됩니다.

조준 방향은 따로 보내지 않아도 됩니다. `GetMuzzleTransform`이 쓰는 컨트롤러의 회전은 캐릭터 무브먼트가 이동 정보와 함께 서버로 계속 보내고 있어서, 서버에서 계산해도 클라이언트가 보는 방향과 거의 같습니다.

빌드하고 클라이언트 창에서 히트스캔으로 쏩니다. 이번에는 상대의 체력이 두 창에서 함께 줄어듭니다. 디버그 선은 서버에서 그려지므로 호스트 창에만 보입니다.

### 33-3 발사체 복제하기

`bUseProjectile`을 켜고 클라이언트 창에서 쏘면 이번에는 **호스트 창에만** 구체가 보입니다. 서버에서 스폰했지만 발사체가 복제되지 않는 액터이기 때문입니다. Chapter 30에서 배운 대로 복제를 켭니다.

```cpp
// MyProjectile.cpp 생성자 안에 추가
bReplicates = true;
SetReplicateMovement(true);
```

부딪혔을 때의 처리도 서버에서만 하게 고칩니다.

```cpp
void AMyProjectile::OnHit(UPrimitiveComponent* HitComponent, AActor* OtherActor,
	UPrimitiveComponent* OtherComp, FVector NormalImpulse, const FHitResult& Hit)
{
	if (!HasAuthority())
	{
		return;
	}

	if (OtherActor != nullptr && OtherActor != this)
	{
		UGameplayStatics::ApplyDamage(OtherActor, Damage, GetInstigatorController(), this, UDamageType::StaticClass());
	}

	Destroy();
}
```

**코드 해석**

- `bReplicates = true`는 서버에서 스폰한 이 액터를 클라이언트에도 만들어 줍니다.
- `SetReplicateMovement(true)`는 위치와 속도도 함께 보냅니다. 클라이언트의 구체가 서버와 같은 길로 날아갑니다.
- 발사체는 각 컴퓨터에서 따로 날아가므로 `OnHit`도 컴퓨터마다 호출됩니다. 가드가 없으면 클라이언트가 복제된 액터를 스스로 없애려다 실패하고 경고가 찍힙니다. 서버에서 `Destroy`하면 클라이언트의 구체도 함께 사라집니다.

스폰하는 쪽은 고칠 것이 없습니다. `FireProjectile`은 이미 서버에서만 호출되기 때문입니다. 클라이언트가 직접 스폰하면 그 구체는 그 창에만 있는 가짜가 되어, 맞아도 데미지가 없고 다른 사람에게 보이지도 않습니다.

### 33-4 소리와 이펙트는 Multicast로

Chapter 29에서 `Fire` 안에 넣었던 총성과 명중 이펙트는 이제 서버에서만 재생됩니다. 호스트 창에서만 소리가 나고 클라이언트는 아무것도 듣지 못합니다. 모든 컴퓨터에서 재생되게 Multicast RPC로 옮깁니다.

```cpp
// MyCharacter.h
private:
	UFUNCTION(NetMulticast, Unreliable)
	void Multicast_PlayFireFX(bool bHit, FVector ImpactPoint, FVector ImpactNormal);
```

```cpp
// MyCharacter.cpp
void AMyCharacter::Multicast_PlayFireFX_Implementation(bool bHit, FVector ImpactPoint, FVector ImpactNormal)
{
	if (FireSound != nullptr)
	{
		UGameplayStatics::PlaySoundAtLocation(this, FireSound, GetActorLocation());
	}

	if (bHit && ImpactFX != nullptr)
	{
		UNiagaraFunctionLibrary::SpawnSystemAtLocation(
			GetWorld(), ImpactFX, ImpactPoint, ImpactNormal.Rotation());
	}
}
```

`FireHitscan`에서 소리와 이펙트를 재생하던 줄을 지우고, 데미지를 준 다음에 한 줄을 넣습니다.

```cpp
// FireHitscan 함수의 끝
Multicast_PlayFireFX(bHit, Hit.ImpactPoint, Hit.ImpactNormal);
```

`FireProjectile`의 끝에는 `Multicast_PlayFireFX(false, FVector::ZeroVector, FVector::ZeroVector);`를 넣어 총성만 재생합니다.

**코드 해석**

- Multicast RPC는 서버에서 호출하면 서버와 모든 클라이언트에서 `_Implementation`이 실행됩니다.
- RPC는 매개변수를 함께 보낼 수 있습니다. 서버가 계산한 명중 위치를 그대로 전달하므로 모든 창에서 같은 곳에 불꽃이 튑니다.
- `Unreliable`로 두었습니다. 총성 한 번이 빠져도 게임 결과는 달라지지 않습니다.

역할을 표로 정리하면 다음과 같습니다.

| 처리 | 어디서 | 전달 방법 |
|---|---|---|
| 쏠 수 있는지 판단 | 서버 | Server RPC로 요청받는다 |
| 판정과 데미지 | 서버 | 체력이 RepNotify로 전달된다 |
| 발사체 | 서버에서 스폰 | 액터 복제 |
| 소리와 이펙트 | 모든 컴퓨터 | Multicast RPC |

결과에 영향을 주는 것은 서버 한 곳에서, 보이고 들리기만 하는 것은 각자의 컴퓨터에서 처리합니다.

### 33-5 근접 공격에 적용하기

Chapter 20의 콤보 공격도 같은 원칙으로 나눕니다. 공격 동작은 모든 컴퓨터에서 보여야 하고, 판정과 데미지는 서버에서만 일어나야 합니다.

```cpp
// MyCharacter.h
private:
	UFUNCTION(Server, Reliable)
	void Server_Attack();

	UFUNCTION(NetMulticast, Reliable)
	void Multicast_Attack();

	void DoAttack();
```

```cpp
// MyCharacter.cpp
void AMyCharacter::Attack()
{
	Server_Attack();
}

void AMyCharacter::Server_Attack_Implementation()
{
	Multicast_Attack();
}

void AMyCharacter::Multicast_Attack_Implementation()
{
	DoAttack();
}
```

`DoAttack`에는 Chapter 20에서 `Attack`에 있던 콤보 로직을 그대로 옮깁니다.

**코드 해석**

- 입력을 받은 클라이언트가 서버에 요청하고, 서버가 모든 컴퓨터에 "이 캐릭터가 공격한다"고 알립니다.
- 각 컴퓨터는 자기 화면에 있는 그 캐릭터의 `DoAttack`을 실행합니다. `bIsAttacking`과 `CurrentCombo` 같은 콤보 상태를 복제하지 않아도, 같은 입력이 같은 순서로 전달되므로 컴퓨터마다 같은 상태가 됩니다.
- 콤보는 클릭 한 번이 빠지면 동작이 달라지므로 `Reliable`로 둡니다.

판정 함수에는 가드를 넣습니다.

```cpp
void AMyCharacter::AttackCheck()
{
	if (!HasAuthority())
	{
		return;
	}

	// Chapter 21의 스윕과 Chapter 22의 ApplyDamage는 그대로 둔다
}
```

몽타주는 모든 컴퓨터에서 재생되므로 `AttackHitCheck` 노티파이도 모든 컴퓨터에서 발생합니다. 가드가 없으면 컴퓨터마다 따로 스윕해 결과가 어긋납니다. 서버에서만 스윕하고 데미지를 주면, 줄어든 체력은 Chapter 32의 RepNotify로 모두에게 전달됩니다.

호스트 창에서 공격하는 캐릭터가 화면 밖에 있을 때 판정이 일어나지 않는다면, 캐릭터 블루프린트에서 메시 컴포넌트의 **비저빌리티 기반 애님 틱 옵션**을 **Always Tick Pose and Refresh Bones**로 바꿉니다. 화면에 보이지 않는 캐릭터는 엔진이 애니메이션 갱신을 줄여 노티파이가 늦어질 수 있기 때문입니다.

### 33-6 따라하기 — 발사·데미지 동기화

1. 플레이어 수 2, 리슨 서버로 실행해 33-1의 표와 같은 증상을 먼저 확인한다.
2. `Fire`를 `Server_RequestFire`를 거치도록 고치고 `FireHitscan`과 `FireProjectile`로 나눈다.
3. `Server_RequestFire_Implementation`에 `PLOG_S`를 넣고, 클라이언트 창에서 쏘았을 때 `Server` 창의 로그에만 찍히는지 확인한다.
4. 클라이언트 창에서 히트스캔으로 상대를 맞혀 두 창의 체력바가 함께 줄어드는지 확인한다.
5. 발사체에 복제를 켜고 `OnHit`에 가드를 넣는다. 발사체가 두 창에 모두 보이는지 확인한다.
6. 총성과 명중 이펙트를 `Multicast_PlayFireFX`로 옮기고 두 창에서 모두 재생되는지 확인한다.
7. 근접 공격을 33-5처럼 고치고, 클라이언트 창에서 공격했을 때 호스트 창에서도 동작이 보이는지 확인한다.
8. `FireInterval`을 `1.0`으로 바꾸고 연타해도 1초에 한 발만 나가는지 확인한 뒤 되돌린다.

![PIE 두 창에서 같은 발사체 궤적·체력 감소가 보이는 전투 동기화 확인 화면](./img/33-combat-replication-pie.png)

한쪽 창에만 보이는 것이 있으면 아래 순서로 찾습니다.

| 증상 | 먼저 볼 곳 |
|---|---|
| 클라이언트가 쏴도 서버 로그가 없다 | `Fire`가 `Server_RequestFire()`를 호출하는가. 호출한 캐릭터가 내 소유인가 |
| 발사체가 호스트 창에만 보인다 | 발사체 생성자의 `bReplicates` |
| 소리와 이펙트가 호스트 창에만 있다 | 재생 코드가 Multicast 안에 있는가 |
| 체력이 한쪽만 줄어든다 | Chapter 32의 `DOREPLIFETIME`과 `OnRep_CurrentHealth` |

### 연습문제

1. 문제: 발사체를 클라이언트에서 직접 스폰할 때 생기는 문제 두 가지를 쓰세요.
2. 문제: 발사 간격 검사를 클라이언트의 `Fire`가 아니라 서버의 `_Implementation`에 두는 이유를 한 문장으로 쓰세요.
3. 문제: 명중 이펙트를 리플리케이션이 아니라 Multicast RPC로 보내는 이유를 한 문장으로 쓰세요.  
   - 조건: Chapter 30의 상태와 사건 구분을 쓸 것
4. 문제: `AttackCheck`에서 `HasAuthority` 가드를 빼면 생기는 문제를 한 문장으로 쓰세요.

### 정답 포인트

- 다른 플레이어 화면에 보이지 않고, 맞아도 서버의 체력이 줄지 않는다. 클라이언트의 검사는 조작으로 건너뛸 수 있어서 서버가 다시 확인해야 한다. 이펙트는 계속 유지되는 상태가 아니라 한 번 일어나는 사건이다. 컴퓨터마다 따로 스윕해 판정 결과가 서로 어긋난다.

---

[상위 문서로 돌아가기](./README.md)
