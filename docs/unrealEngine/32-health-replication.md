# Chapter 32 체력 리플리케이션

## 학습 목표

- 체력을 서버에서만 변경해야 하는 이유를 설명할 수 있다.
- `Replicated`와 `ReplicatedUsing`의 차이를 구분해 쓸 수 있다.
- RepNotify 함수로 복제된 체력을 UI에 반영할 수 있다.

## 본문

### 32-1 서버가 체력을 소유한다

Chapter 29의 게임을 플레이어 수 2, 리슨 서버로 실행해 봅니다. 한 창에서 상대를 때리면 그 창에서만 체력이 줄고, 맞은 사람의 창에서는 체력바가 그대로입니다.

각 컴퓨터가 자기 화면에서만 `CurrentHealth`를 바꾸고 있기 때문입니다. 서로 값이 어긋나고, 클라이언트가 자기 체력을 마음대로 고칠 수 있는 구조이기도 합니다.

Chapter 30에서 배운 원칙대로 고칩니다.

1. 맞았는지 판정하는 일은 서버에서 한다.
2. 서버만 체력을 줄인다.
3. 줄어든 체력이 리플리케이션으로 클라이언트에 전달된다.
4. 클라이언트의 UI는 전달받은 값을 보여 준다.

### 32-2 서버에서만 실행하기

코드가 지금 서버에서 실행되고 있는지는 `HasAuthority()`로 확인합니다. 이 액터에 대한 최종 권한이 이 컴퓨터에 있으면 참을 돌려줍니다. 서버에서 스폰되어 복제된 액터는 서버에서만 참입니다.

체력 컴포넌트의 함수 맨 앞에 확인을 넣습니다.

```cpp
// HealthComponent.cpp
void UHealthComponent::ReduceHealth(float Amount)
{
	if (!GetOwner()->HasAuthority())
	{
		return;
	}

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
```

`GetOwner()`는 이 컴포넌트가 붙어 있는 액터입니다. 컴포넌트에는 `HasAuthority`가 없으므로 주인 액터에게 묻습니다. `Heal`에도 같은 확인을 넣습니다. 이렇게 조건을 걸어 잘못된 곳에서 실행되지 않게 막는 코드를 **가드**라고 부릅니다.

### 32-3 체력 복제하기

서버에서 바뀐 값을 클라이언트로 보냅니다. Chapter 30의 `Replicated`를 쓰면 값은 전달되지만, 클라이언트는 값이 바뀌었다는 사실을 알 방법이 없어 체력바를 갱신하지 못합니다.

**RepNotify**는 복제된 값이 도착했을 때 지정한 함수를 호출해 주는 기능입니다. 지정자를 `Replicated` 대신 `ReplicatedUsing = 함수이름`으로 씁니다.

| 지정자 | 값 전달 | 도착 알림 |
|---|---|---|
| `Replicated` | 된다 | 없다 |
| `ReplicatedUsing = OnRep_함수` | 된다 | 클라이언트에서 `OnRep_함수`가 호출된다 |

```cpp
// HealthComponent.h 의 클래스 안
public:
	UHealthComponent();

	virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;

private:
	UFUNCTION()
	void OnRep_CurrentHealth();

	UPROPERTY(ReplicatedUsing = OnRep_CurrentHealth, VisibleInstanceOnly, Category = Health)
	float CurrentHealth = 100.0f;
```

```cpp
// HealthComponent.cpp
#include "Net/UnrealNetwork.h"

UHealthComponent::UHealthComponent()
{
	SetIsReplicatedByDefault(true);
}

void UHealthComponent::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);
	DOREPLIFETIME(UHealthComponent, CurrentHealth);
}

void UHealthComponent::OnRep_CurrentHealth()
{
	OnHealthChanged.Broadcast(CurrentHealth, MaxHealth);
}
```

**코드 해석**

- `SetIsReplicatedByDefault(true)`는 이 컴포넌트를 복제 대상으로 만듭니다. 액터의 `bReplicates`에 해당하는 컴포넌트용 설정입니다. 캐릭터는 처음부터 복제가 켜져 있으므로 액터 쪽은 따로 건드리지 않습니다.
- `DOREPLIFETIME`으로 `CurrentHealth`를 복제 목록에 등록합니다.
- `OnRep_CurrentHealth`는 클라이언트에 새 값이 도착하면 호출됩니다. 여기서 `OnHealthChanged`를 방송하면 Chapter 25에서 만든 체력바 위젯이 그대로 반응합니다. RepNotify 함수에는 `UFUNCTION()`이 필요합니다.

RepNotify 함수는 **클라이언트에서만** 자동으로 호출됩니다. 서버에서는 호출되지 않으므로, 서버는 32-2의 `ReduceHealth` 안에서 직접 `OnHealthChanged`를 방송합니다. 리슨 서버의 호스트도 화면이 있는 플레이어이기 때문에 이 방송이 있어야 호스트의 체력바가 움직입니다.

### 32-4 사망 처리 나누기

체력이 0이 되면 Chapter 29의 `HandleDeath`가 `Destroy()`를 호출합니다. `OnDeath`는 서버의 `ReduceHealth`에서만 방송되므로 `Destroy`도 서버에서만 실행됩니다. 복제되는 액터를 서버에서 없애면 모든 클라이언트에서도 함께 사라집니다.

점수를 올리는 게임모드의 `OnCharacterDead`도 서버에서만 실행됩니다. 게임모드는 서버에만 존재하기 때문입니다. 판정과 규칙이 모두 서버 한 곳에서 일어나게 되었습니다.

### 32-5 복제할 것과 하지 않을 것

살아 있는지 여부나 팀 번호처럼 작은 상태도 같은 방법으로 복제합니다. 하지만 모든 변수를 복제하지는 않습니다. 복제는 통신량을 쓰기 때문입니다.

| 복제한다 | 복제하지 않는다 |
|---|---|
| 다른 플레이어도 알아야 하는 값 | 한 사람의 화면에만 필요한 값 |
| 체력, 탄약, 점수, 팀 | 조준점 위치, 메뉴가 열렸는지, 카메라 흔들림 |

콤보 상태를 담은 `bIsAttacking`이나 `CurrentCombo`는 지금 복제되지 않습니다. 공격 동작과 판정의 동기화는 Chapter 33에서 다룹니다.

### 32-6 따라하기 — 체력 동기화

1. `ReduceHealth`와 `Heal`에 `HasAuthority` 가드를 넣는다.
2. `CurrentHealth`에 `ReplicatedUsing`을 붙이고 `DOREPLIFETIME`으로 등록한다.
3. 생성자에서 `SetIsReplicatedByDefault(true)`를 호출한다.
4. `OnRep_CurrentHealth`에서 `OnHealthChanged`를 방송한다.
5. 플레이어 수 2, 리슨 서버로 실행한다.
6. Chapter 29의 함정을 클라이언트 캐릭터로 밟아 두 창에서 체력이 같은 값으로 줄어드는지 확인한다.
7. `OnRep_CurrentHealth`에 `PLOG`를 넣어 클라이언트 창에서만 찍히는지 확인한다.

함정의 오버랩 이벤트는 서버와 클라이언트 양쪽에서 일어나지만, 가드 덕분에 서버에서만 체력이 줄어듭니다.

### 연습문제

1. 문제: 체력을 클라이언트에서 직접 줄일 때 생기는 문제 두 가지를 쓰세요.
2. 문제: 체력 변수에 `Replicated`가 아니라 `ReplicatedUsing`을 쓰는 이유를 한 문장으로 쓰세요.  
   - 조건: 체력바를 언급할 것
3. 문제: 복제하지 않아도 되는 값의 예를 하나 들고 이유를 한 문장으로 쓰세요.

### 정답 포인트

- 컴퓨터마다 값이 어긋나고 조작으로 속일 수 있다. 값이 도착한 순간 함수가 호출되어야 체력바를 갱신할 수 있어서. 조준점 위치처럼 한 사람의 화면에만 필요한 값은 다른 컴퓨터가 알 필요가 없다.

---

[상위 문서로 돌아가기](./README.md)
