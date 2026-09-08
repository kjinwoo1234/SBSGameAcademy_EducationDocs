# Chapter 08 FPS 시스템 통합

## 학습 목표

- 체력 증감과 데미지 오브젝트를 기존 FPS에 연결할 수 있다.
- 사운드·이펙트를 전투 이벤트에 붙여 재생·스폰할 수 있다.
- 싱글 플레이 미니 게임으로서 한 판이 끝나는 흐름을 완성할 수 있다.

## 본문

### 08-1 체력 증감 함수

플레이어와 NPC 모두 **최대 체력·현재 체력**을 갖고, 피해와 회복 함수를 한곳으로 모읍니다. UI는 이 함수가 끝난 뒤 갱신합니다.

```cpp
void UHealthComponent::ApplyDamage(float Amount)
{
	if (Amount <= 0.0f || CurrentHealth <= 0.0f)
	{
		return;
	}
	CurrentHealth = FMath::Clamp(CurrentHealth - Amount, 0.0f, MaxHealth);
	OnHealthChanged.Broadcast(CurrentHealth, MaxHealth);
	if (CurrentHealth <= 0.0f)
	{
		OnDeath.Broadcast();
	}
}

void UHealthComponent::Heal(float Amount)
{
	if (Amount <= 0.0f || CurrentHealth <= 0.0f)
	{
		return;
	}
	CurrentHealth = FMath::Clamp(CurrentHealth + Amount, 0.0f, MaxHealth);
	OnHealthChanged.Broadcast(CurrentHealth, MaxHealth);
}
```

힐 픽업은 Overlap 시 `Heal`을 호출하면 됩니다. 함정은 Overlap 또는 Tick 근접 시 `ApplyDamage`를 호출합니다.

### 08-2 데미지 오브젝트

맵에 두는 **데미지 볼륨·가시 가시덤불·폭발 통**은 “맞으면 데미지”만 책임지는 액터로 둡니다. 플레이어 입력과 섞지 않습니다.

히트스캔이 통을 맞추면 통이 폭발하고, 반경 트레이스 또는 Overlap으로 주변 `HealthComponent`에 데미지를 전달하는 식이 읽기 쉽습니다.

### 08-3 사운드·이펙트 붙이기

전투 피드백을 이벤트에 묶습니다.

| 이벤트 | 피드백 예 |
|---|---|
| 발사 | 총성, 총구 이펙트 |
| 명중 | 임팩트 사운드·파티클 |
| 피격 | 히트음, 화면 깜빡임 |
| 처치 | 점수 UI, 짧은 효과음 |
| 승리·패배 | 결과 위젯, 입력 정지 |

![발사 순간 총구 이펙트와 히트 지점 임팩트 파티클이 레벨에 보이는 플레이 화면](./img/08-muzzle-impact-fx.png)

사운드 최소 호출:

```cpp
if (FireSound)
{
	UGameplayStatics::PlaySoundAtLocation(this, FireSound, GetActorLocation());
}
```

이펙트는 프로젝트에 있는 **Niagara** 또는 구형 Cascade 시스템을 히트 지점에 스폰합니다.

```cpp
if (ImpactFX)
{
	UNiagaraFunctionLibrary::SpawnSystemAtLocation(
		GetWorld(), ImpactFX, Hit.ImpactPoint, Hit.ImpactNormal.Rotation());
}
```

엔진·플러그인에 따라 Cascade용 `SpawnEmitterAtLocation`을 쓸 수도 있습니다. 이름은 프로젝트 에셋에 맞게 고르면 됩니다. 중요한 것은 **이벤트 시점**에 재생·스폰을 호출하는 일입니다.

### 08-4 한 판 흐름과 폴리싱

GameMode의 종료 조건이 참이 되면 결과 UI를 띄우고, 재시작은 레벨을 다시 로드하거나 커스텀 리셋 함수를 호출합니다.

폴리싱 점검:

1. 이동·조준·발사가 한 입력 체계로 동작하는가
2. NPC 처치 → 점수 → 목표 달성 → 결과 UI가 끊기지 않는가
3. 피격·발사에 소리·이펙트가 붙는가
4. 체력 0에서 입력·카메라가 이상하지 않은가
5. 재시작 후 점수·체력이 초기화되는가

### 08-5 따라하기 — FPS 미니 게임 완성

1. Chapter 02~07 기능을 한 맵에 모은다.
2. 체력 컴포넌트·데미지 오브젝트·힐 픽업을 배치한다.
3. 발사·명중·처치에 사운드·이펙트를 연결한다.
4. 목표 점수 또는 시간 제한으로 한 판을 끝낸다.
5. 결과 UI와 재시작을 확인한다.
6. 위 폴리싱 점검 다섯 항을 통과시킨다.

이 장으로 **싱글 FPS 미니 게임**이 한 바퀴 돕니다. Chapter 09부터는 같은 경험을 멀티플레이로 확장합니다.

### 연습문제

1. 문제: 체력 변경 후 UI를 갱신하기 좋은 시점과 방법을 한 문장으로 쓰세요.  
2. 문제: 데미지 볼륨을 플레이어 캐릭터 클래스 안에 넣지 않는 이유를 한 문장으로 쓰세요.  
3. 문제: 싱글 미니 게임 “한 판”이 끝나려면 필요한 요소 세 가지를 목록으로 쓰세요.

### 정답 포인트

- 체력 함수 끝에서 델리게이트·UI 갱신. 맵 오브젝트는 재사용·책임 분리. 목표 규칙·피드백·종료 UI.

---

[상위 문서로 돌아가기](./README.md)
