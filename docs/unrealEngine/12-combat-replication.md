# Chapter 12 전투 리플리케이션

## 학습 목표

- 발사 요청을 Server RPC로 보내는 이유를 설명할 수 있다.
- 발사체를 서버에서 스폰하고 복제되게 맞출 수 있다.
- 데미지 적용과 FX Multicast를 역할별로 나눌 수 있다.

## 본문

### 12-1 발사 요청

로컬에서 마우스 클릭을 받으면 바로 데미지를 넣지 않습니다. **Server RPC**로 “발사하겠다”고 알립니다.

호출할 때는 Chapter 09에서 선언한 이름 그대로 `Server_RequestFire()`를 씁니다. 서버에서 실제로 돌릴 본문은 엔진 규칙으로 **`함수이름_Implementation`** 에 둡니다. 호출 이름과 본문 함수 이름이 달라 보이지만, 같은 Server RPC의 선언과 구현입니다. 오너십이 있는 내 캐릭터에서 호출해야 합니다.

```cpp
void AMyCharacter::OnFirePressed()
{
	Server_RequestFire();
	// 선택: 로컬만 즉시 총구 FX
}

void AMyCharacter::Server_RequestFire_Implementation()
{
	if (!CanFire())
	{
		return;
	}
	// 서버: 쿨다운·탄약 검사 후 트레이스 또는 발사체 스폰
	ConsumeAmmo();
	SpawnProjectileOrTrace();
}
```

서버는 쿨다운·탄약·팀킬 규칙을 검사한 뒤 트레이스하거나 발사체를 스폰합니다.

### 12-2 발사체 리플리케이션

발사체 액터는 `bReplicates = true`로 두고 **서버에서만** 스폰합니다. 클라이언트가 로컬에만 스폰하면 다른 플레이어 화면에 없거나 판정이 엇갈립니다.

```cpp
void AMyCharacter::SpawnProjectileOrTrace()
{
	if (!HasAuthority() || !ProjectileClass)
	{
		return;
	}

	FActorSpawnParameters Params;
	Params.Owner = this;
	Params.Instigator = GetInstigator();
	Params.SpawnCollisionHandlingOverride =
		ESpawnActorCollisionHandlingMethod::AlwaysSpawn;

	AMyProjectile* Proj = GetWorld()->SpawnActor<AMyProjectile>(
		ProjectileClass, GetMuzzleTransform(), Params);
	// Projectile Movement가 속도를 담당. 액터 bReplicates = true
}
```

발사체 클래스 쪽 최소 설정:

1. 생성자에서 `bReplicates = true`를 켠다.
2. 필요하면 `SetReplicateMovement(true)`로 위치를 맞춘다.
3. Hit·Overlap 데미지는 **서버만** 적용한다. `HasAuthority` 가드.
4. 이펙트는 Multicast 또는 히트 시 로컬 재생으로 나눈다.

히트스캔이면 서버 트레이스 결과로 데미지를 넣고, 이펙트만 Multicast로 재생해도 됩니다.

### 12-3 데미지와 FX 분리

| 처리 | 어디서 | 왜 |
|---|---|---|
| 데미지·처치 | 서버 | 점수·체력의 기준 |
| 총구·임팩트 FX | Multicast 또는 로컬 예측 | 보이기용, 판정과 분리 |

클라이언트가 FX만 먼저 보여 주고 서버 결과가 오면 체력을 RepNotify로 맞추는 식도 가능합니다. 수업에서는 **서버 확정 데미지 + Multicast FX**부터 안정화합니다.

### 12-4 따라하기 — 발사·데미지 동기화

1. PIE 플레이어 2, Listen Server로 실행한다.
2. 클라이언트 창에서 발사한다. 서버 로그에 `_Implementation`이 찍히는지 확인한다.
3. 발사체가 **양쪽 창**에 보이는지 확인한다. 한쪽만 보이면 스폰 권한·`bReplicates`를 점검한다.
4. 맞은 NPC 체력이 양쪽에서 같이 줄어드는지 확인한다.
5. FX가 한쪽만 보이면 Multicast·스폰 위치를 점검한다.

![PIE 두 창에서 같은 발사체 궤적·체력 감소가 보이는 전투 동기화 확인 화면](./img/12-combat-replication-pie.png)

### 연습문제

1. 문제: 발사체 스폰을 클라이언트에서만 할 때 생기는 문제를 한 문장으로 쓰세요.  
2. 문제: Server RPC 구현부에서 발사 전에 검사할 것 두 가지를 쓰세요.  
3. 문제: 데미지와 Multicast FX를 나누는 이유를 한 문장으로 쓰세요.

### 정답 포인트

- 다른 클라에 안 보이거나 판정 불일치. 탄약·쿨다운. 판정은 권한, FX는 표현.

---

[상위 문서로 돌아가기](./README.md)
