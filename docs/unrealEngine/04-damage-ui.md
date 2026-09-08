# Chapter 04 데미지·UI

## 학습 목표

- NPC에 데미지를 적용하고 처치 흐름을 만들 수 있다.
- `PlayerState` 점수와 `GameMode` 종료 조건의 역할을 구분할 수 있다.
- 위젯으로 체력바를 만들고 **변수 바인딩** 또는 **델리게이트**로 연동할 수 있다.

## 본문

### 04-1 데미지를 받는 NPC

언리얼에는 `UGameplayStatics::ApplyDamage`와 `TakeDamage` 경로가 있습니다. 수업용으로는 NPC 액터에 **현재 체력**을 두고, 히트 시 체력을 깎는 방식부터 갑니다.

```cpp
UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Health")
float MaxHealth;

UPROPERTY(BlueprintReadOnly, Category = "Health")
float Health;

void ANPC::ReceiveHitDamage(float Amount)
{
	Health = FMath::Clamp(Health - Amount, 0.0f, MaxHealth);
	if (Health <= 0.0f)
	{
		Destroy();
	}
}
```

플레이어 트레이스가 NPC를 맞추면 `ReceiveHitDamage`를 호출합니다. 나중에 네트워크에서는 서버만 체력을 바꾸게 바꿉니다. 지금은 싱글 기준으로 충분합니다.

### 04-2 PlayerState 점수 · GameMode 종료

**PlayerState**는 플레이어에 붙는 상태입니다. 점수처럼 라운드 동안 유지할 값에 잘 맞습니다. Pawn이 죽어도 PlayerState는 남는 경우가 많습니다.

**GameMode**는 규칙의 중심입니다. “점수 N점 도달 시 게임 종료” 같은 조건을 여기에 둡니다.

1. NPC 처치 시 해당 플레이어의 `PlayerState` 점수를 올린다.
2. `GameMode`가 점수를 확인한다.
3. 목표에 도달하면 게임을 멈추거나 결과 UI를 연다.

```cpp
void AMyPlayerState::AddScore(int32 Delta)
{
	Score += Delta;
}

void AMyGameMode::CheckWin(AMyPlayerState* PS)
{
	if (PS && PS->GetScore() >= WinScore)
	{
		// 종료 처리: 입력 잠그기, 결과 위젯 표시 등
	}
}
```

### 04-3 위젯 기초와 체력바

UMG **위젯 블루프린트**로 HUD를 만듭니다.

![위젯 디자이너 Canvas에 Progress Bar를 배치하고 앵커를 잡은 HUD 레이아웃](./img/04-umg-healthbar-designer.png)

1. 위젯 블루프린트를 만든다.
2. Canvas에 Progress Bar를 둔다. 이름은 예: `HealthBar`.
3. Progress Bar를 **Is Variable**로 두어 그래프·C++에서 접근 가능하게 한다.
4. 앵커를 화면 모서리·상단에 맞춰 해상도가 바뀌어도 위치가 크게 흔들리지 않게 한다.
5. 플레이어 컨트롤러 `BeginPlay`에서 위젯을 만들고 Viewport에 추가한다.

```cpp
void AMyPlayerController::BeginPlay()
{
	Super::BeginPlay();
	if (IsLocalController() && HudClass)
	{
		HudWidget = CreateWidget<UUserWidget>(this, HudClass);
		if (HudWidget)
		{
			HudWidget->AddToViewport();
		}
	}
}
```

체력바는 **로컬 플레이어만** 자기 HUD를 띄우면 됩니다. `IsLocalController` 검사가 그 역할입니다.

### 04-4 변수 바인딩으로 Percent 연결

**변수 바인딩**은 위젯 디자이너에서 Progress Bar의 **Percent**에 함수나 변수를 묶어, 그릴 때마다 값을 읽게 하는 방법입니다.

블루프린트 위젯에서:

1. Progress Bar를 선택한다.
2. Details의 Percent 옆 **Bind**를 누른다.

![Progress Bar Details에서 Percent 옆 Bind 메뉴가 열린 화면](./img/04-progress-bar-percent-bind.png)

3. 함수를 새로 만들거나 기존 함수를 고른다.
4. 함수에서 플레이어 체력 `Current / Max`를 0~1로 계산해 반환한다.

C++ 위젯을 쓸 때는 `NativeTick` 또는 전용 갱신 함수에서 `HealthBar->SetPercent(Current / Max)`를 호출해도 같은 결과입니다. 바인딩은 “디자이너에서 Percent를 데이터에 연결한다”는 개념이고, Tick·함수 갱신은 “코드로 Percent를 넣는다”는 개념입니다.

### 04-5 델리게이트로 UI 연동

**델리게이트**는 “체력이 바뀌었다”고 알리는 방송입니다. 캐릭터가 방송하고, 위젯이 구독합니다. 체력을 깎는 코드와 UI 코드를 직접 섞지 않아도 됩니다.

```cpp
DECLARE_DYNAMIC_MULTICAST_DELEGATE_TwoParams(FOnHealthChanged, float, Current, float, Max);

UPROPERTY(BlueprintAssignable)
FOnHealthChanged OnHealthChanged;

void UHealthComponent::ApplyDamage(float Amount)
{
	CurrentHealth = FMath::Clamp(CurrentHealth - Amount, 0.0f, MaxHealth);
	OnHealthChanged.Broadcast(CurrentHealth, MaxHealth);
}
```

위젯 쪽:

1. HUD가 열린 뒤 소유 폰·체력 컴포넌트를 찾는다.
2. `OnHealthChanged`에 위젯 함수를 `AddDynamic`으로 묶는다.
3. 콜백에서 `HealthBar->SetPercent(Current / Max)`를 호출한다.
4. 위젯이 닫힐 때 `RemoveDynamic`으로 풀어 누수를 줄인다.

![위젯 이벤트 그래프에서 OnHealthChanged에 바인드하고 Set Percent를 호출하는 노드 예시](./img/04-widget-delegate-bind-graph.png)

초보 단계에서는 Tick으로 Percent를 갱신해도 동작합니다. 다만 커리큘럼에서 말하는 **변수 바인딩·델리게이트 UI 연동**은 위 두 경로입니다. 체력이 바뀌는 순간에만 갱신하려면 델리게이트가 더 맞습니다.

### 04-6 따라하기 — NPC·점수·체력 UI

1. NPC에 `Health`/`MaxHealth`와 `ReceiveHitDamage`를 넣는다.
2. 히트스캔 명중 시 데미지를 호출하고, 0이 되면 사라지는지 확인한다.
3. 처치 시 `PlayerState` 점수를 올리고, `GameMode`에서 목표 점수면 로그 “Win”을 남긴다.
4. HUD 위젯에 Progress Bar를 두고 Viewport에 올린다.
5. Percent를 **Bind**하거나, 델리게이트/`SetPercent`로 체력 비율을 연결한다.
6. 피격·회복 후 바가 움직이는지 확인한다.

이 장까지면 싱글 FPS의 전투·점수·UI 뼈대가 갖춰집니다. Chapter 05에서 기획을 잡고 기능을 고릅니다.

### 연습문제

1. 문제: 점수를 Pawn이 아니라 PlayerState에 두는 이유를 한 문장으로 쓰세요.  
2. 문제: 변수 바인딩과 델리게이트 연동의 차이를 두 문장으로 쓰세요.  
3. 문제: HUD 위젯을 Viewport에 올릴 때 로컬 컨트롤러만 고르는 이유를 한 문장으로 쓰세요.

### 정답 포인트

- Pawn 교체·사망과 점수 분리. 바인딩=그릴 때 읽기, 델리게이트=변경 순간 알림. 원격마다 HUD 겹침 방지.

---

[상위 문서로 돌아가기](./README.md)
