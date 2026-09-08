# Chapter 03 전투·애니메이션

## 학습 목표

- 발사체 방식과 히트스캔 방식의 차이를 설명하고 각각 최소 구현을 연결할 수 있다.
- 라인 트레이스로 명중을 판정하는 흐름을 작성할 수 있다.
- Mixamo 등 외부 클립을 가져와 리타겟팅하는 순서를 따라갈 수 있다.
- 애니메이션 블루프린트와 몽타주의 역할을 구분해 적용할 수 있다.

## 본문

### 03-1 발사체와 히트스캔

FPS에서 총을 구현하는 대표 방법은 두 가지입니다.

| 방식 | 동작 | 잘 맞는 경우 |
|---|---|---|
| **발사체** | 총알 액터·컴포넌트가 날아가며 충돌 | 로켓, 느린 투사체, 궤적이 보이는 무기 |
| **히트스캔** | 그 순간 광선으로 명중 검사 | 대부분의 총, 판정이 즉시여야 할 때 |

### 03-2 히트스캔 — 라인 트레이스

히트스캔은 보통 **라인 트레이스**로 구현합니다. 카메라 또는 총구에서 전방으로 선을 긋고, 처음 맞은 액터를 확인합니다.

```cpp
FHitResult Hit;
const FVector Start = GetPawn()->GetActorLocation();
const FVector End = Start + GetControlRotation().Vector() * 10000.0f;

FCollisionQueryParams Params;
Params.AddIgnoredActor(this);
Params.AddIgnoredActor(GetPawn());

const bool bHit = GetWorld()->LineTraceSingleByChannel(
	Hit, Start, End, ECC_Visibility, Params);

if (bHit)
{
	AActor* HitActor = Hit.GetActor();
	// 데미지 적용은 Chapter 04에서 이어서 다룸
}
```

**코드 해석**

- `Start`~`End`가 검사 구간입니다.
- `AddIgnoredActor`로 자신·내 폰을 빼 자기 맞춤을 줄입니다.
- 채널은 프로젝트 충돌 설정에 맞게 고릅니다. `Visibility`는 예시입니다.

### 03-3 발사체 — 최소 구현

발사체는 전용 액터에 **Projectile Movement** 컴포넌트를 붙이는 방식이 흔합니다.

1. `AActor` 파생 클래스 `AMyProjectile`를 만든다.
2. 루트에 Sphere Collision, 자식에 Static Mesh를 둔다.
3. `UProjectileMovementComponent`를 추가하고 Initial Speed·Projectile Gravity Scale을 설정한다.
4. Overlap 또는 Hit에서 데미지를 주고 자신을 `Destroy`한다.
5. 무기·캐릭터에서 총구 트랜스폼으로 `SpawnActor`한다.

```cpp
FActorSpawnParameters SpawnParams;
SpawnParams.Owner = this;
SpawnParams.Instigator = GetInstigator();

const FTransform Muzzle = GetMuzzleTransform(); // 총구 소켓·씬 컴포넌트
GetWorld()->SpawnActor<AMyProjectile>(ProjectileClass, Muzzle, SpawnParams);
```

지금은 싱글 플레이 기준으로 로컬에서 스폰하면 됩니다. 네트워크에서는 **서버에서 스폰**하는 패턴을 나중에 맞춥니다.

![발사체 액터 블루프린트: Sphere Collision·Mesh·Projectile Movement 컴포넌트 계층](./img/03-projectile-components.png)

### 03-4 Mixamo · 리타겟팅

**Mixamo**는 캐릭터 메시와 애니메이션 클립을 받을 수 있는 외부 에셋 소스입니다. 수업에서는 “Mixamo에서 받은 FBX를 프로젝트에 넣고, 우리 Skeleton에 맞게 옮긴다”는 흐름만 익히면 됩니다. 사이트 주소는 문서에 넣지 않습니다. 강사·실습 안내에 따릅니다.

권장 순서:

1. Mixamo에서 캐릭터 또는 애니메이션 FBX를 받는다. 수업에서는 **Without Skin** 애니만 받거나, 캐릭터+애니를 구분해 받는 방식을 따른다.
2. 언리얼 **Import**로 FBX를 넣는다. Skeleton·Animation 옵션은 엔진 버전 임포트 창에 맞게 고른다.
3. 플레이어가 쓸 **목표 Skeleton**을 정한다. 템플릿 캐릭터 Skeleton이어도 된다.
4. 가져온 애니의 Skeleton이 목표와 다르면 **IK Retargeter**로 리타겟팅한다. **리타겟팅**은 한 스켈레톤의 움직임을 다른 스켈레톤에 맞게 옮기는 작업이다.

![IK Retargeter 에디터에서 소스·타깃 스켈레톤과 미리보기 포즈가 보이는 화면](./img/03-ik-retargeter.png)

5. 결과 Animation Sequence가 목표 Skeleton을 쓰는지 확인한다.
6. 캐릭터 메시·Anim Blueprint가 같은 Skeleton 계열인지 확인한다.

체크리스트:

1. 플레이어 메시에 맞는 Skeleton을 정한다.
2. 애니메이션 시퀀스가 그 Skeleton을 쓰는지 확인한다.
3. 다른 골격 에셋이면 리타겟 후 프로젝트 Skeleton으로 맞춘다.

### 03-5 애니메이션 블루프린트와 몽타주

**애니메이션 블루프린트**는 매 프레임 포즈를 계산합니다.

따라가기 요약:

1. 캐릭터 메시용 Anim Blueprint를 만든다. 부모 클래스·목표 Skeleton을 맞춘다.
2. Event Graph에서 속도·지면 여부 같은 변수를 갱신한다.
3. Anim Graph에 **State Machine**을 두고 Idle / Run / Jump 상태를 만든다.

![Anim Blueprint Anim Graph의 State Machine: Idle·Run 상태와 전환 화살표](./img/03-anim-bp-state-machine.png)

4. 상태 전환 규칙에 속도 임계값을 넣는다.
5. 캐릭터 메시의 Anim Class에 이 블루프린트를 지정한다.

**몽타주**는 공격처럼 한동안 끼워 넣는 클립 묶음입니다. 기본 로코모션 위에 슬롯으로 재생하는 경우가 많습니다. Anim Graph의 Slot 이름과 몽타주 에셋의 슬롯 이름을 같게 둡니다.

![애니메이션 몽타주 에셋 에디터에서 클립 구간과 Slot 이름이 보이는 화면](./img/03-anim-montage-editor.png)

```cpp
if (AttackMontage && GetMesh() && GetMesh()->GetAnimInstance())
{
	GetMesh()->GetAnimInstance()->Montage_Play(AttackMontage);
}
```

발사 입력에 몽타주를 붙이면 “쏘는 손”이 보이고, 히트스캔은 입력 시점에 트레이스하면 됩니다. 노티파이로 발사 타이밍을 맞추는 방법은 심화이므로, 먼저 입력 직후 트레이스와 몽타주 동시 재생으로 연결해도 됩니다.

### 03-6 따라하기 — 발사·히트스캔·애니

1. 발사 입력 액션을 만들고 마우스 왼쪽에 매핑한다.
2. 히트스캔이면 입력 시 `LineTraceSingleByChannel`을 호출하고, 맞은 액터 이름을 로그로 확인한다.
3. 발사체면 `AMyProjectile`을 스폰하고 벽에 부딪혀 사라지는지 확인한다.
4. Mixamo 클립 하나를 임포트·리타겟하거나, 템플릿 애니가 있으면 Idle/Run State Machine만 연결한다.
5. 발사 시 `Montage_Play`로 공격 몽타주를 재생한다.

체력·점수·UI는 Chapter 04에서 다룹니다.

### 연습문제

1. 문제: 히트스캔을 고르기 좋은 무기 예를 하나 들고, 이유를 한 문장으로 쓰세요.  
2. 문제: Mixamo 클립을 쓴 뒤 포즈가 깨질 때 먼저 확인할 것 두 가지를 쓰세요.  
3. 문제: State Machine과 Montage의 역할 차이를 두 문장으로 쓰세요.

### 정답 포인트

- 즉시 판정 총 → 히트스캔. Skeleton·리타겟 결과. State Machine=상시 로코모션, Montage=끼워 넣는 동작.

---

[상위 문서로 돌아가기](./README.md)
