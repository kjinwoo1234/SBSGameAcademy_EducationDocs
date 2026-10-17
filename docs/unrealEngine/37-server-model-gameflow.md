# Chapter 37 서버 모델·게임 플로우

## 학습 목표

- 리슨 서버와 데디케이티드 서버의 차이를 설명할 수 있다.
- 접속부터 한 판이 끝날 때까지의 흐름을 단계와 담당 클래스로 정리할 수 있다.
- 승패 결과를 모든 플레이어의 화면에 띄우고 호스트가 다시 시작하게 만들 수 있다.
- 리슨 서버로 한 판을 처음부터 끝까지 끊김 없이 플레이해 점검할 수 있다.

## 본문

### 37-1 리슨 서버와 데디케이티드 서버

Chapter 30에서 네트워크 모드 네 가지를 배웠습니다. 서버를 누가 맡는지에 따라 게임의 운영 방식이 달라집니다.

| 모델 | 서버를 맡는 곳 | 잘 맞는 게임 | 약점 |
|---|---|---|---|
| 리슨 서버 | 플레이어 중 한 명의 컴퓨터 | 친구끼리 방을 만드는 소규모 게임 | 호스트가 나가면 방이 끝난다. 호스트만 지연이 없다 |
| 데디케이티드 서버 | 화면 없이 서버 일만 하는 프로그램 | 경쟁 게임, 인원이 많은 게임 | 서버 컴퓨터를 따로 운영해야 한다 |

리슨 서버에서는 호스트의 입력이 네트워크를 거치지 않으므로 호스트가 조금 유리합니다. 데디케이티드 서버는 모든 플레이어가 같은 조건이지만, 서버용 프로그램을 따로 빌드하고 서버 컴퓨터를 계속 켜 두어야 합니다.

두 모델의 게임 코드는 거의 같습니다. Part 04에서 지킨 원칙인 "결과를 바꾸는 일은 서버에서, 보이는 일은 각자의 컴퓨터에서"는 어느 모델에서나 그대로 통합니다. 달라지는 점은 데디케이티드 서버에는 화면과 로컬 플레이어가 없다는 것 하나입니다. 이 과정은 리슨 서버로 마무리합니다.

### 37-2 접속부터 한 판이 끝날 때까지

Part 04에서 만든 조각을 순서대로 놓으면 한 판의 흐름이 됩니다.

| 순서 | 일어나는 일 | 담당 | 배운 곳 |
|---|---|---|---|
| 1 | 프로그램을 켜면 로비 레벨과 로비 위젯이 나온다 | 로비 레벨, `WBP_Lobby` | Chapter 31, 36 |
| 2 | 호스트가 방을 만들고 게임 레벨을 리슨 서버로 연다 | 게임 인스턴스 | Chapter 31 |
| 3 | 참가자가 방을 찾아 들어온다 | 게임 인스턴스 | Chapter 31 |
| 4 | 들어온 플레이어마다 폰과 플레이어 스테이트가 준비된다 | 게임모드 | Chapter 16 |
| 5 | 입력이 Server RPC로 서버에 전달된다 | 캐릭터 | Chapter 33, 35 |
| 6 | 서버가 판정하고 체력과 점수가 복제된다 | 체력 컴포넌트, 플레이어 스테이트 | Chapter 32, 36 |
| 7 | 각자의 화면에 UI가 갱신된다 | 플레이어 컨트롤러가 띄운 위젯 | Chapter 36 |
| 8 | 승패가 정해지고 결과가 모두에게 표시된다 | 게임모드, 게임 스테이트 | 이 장 |
| 9 | 호스트가 다시 시작한다 | 플레이어 컨트롤러, 게임모드 | 이 장 |

8번과 9번이 아직 멀티플레이에 맞지 않습니다. 이 장에서 고칩니다.

### 37-3 결과를 게임 스테이트로 알리기

Chapter 29에서는 목표 점수에 도달하면 게임모드가 처치한 플레이어의 컨트롤러로 결과 위젯을 만들었습니다. 멀티플레이에서는 두 가지가 문제입니다.

- 게임모드는 서버에 있습니다. 처치한 사람이 클라이언트라면, 서버에 있는 그 사람의 컨트롤러로 위젯을 만들어도 그 사람의 화면에는 아무것도 뜨지 않습니다.
- 이긴 사람에게만 알리고 있습니다. 진 사람의 화면에도 결과가 떠야 합니다.

"경기가 끝났고 누가 이겼다"는 모두가 알아야 하는 경기 상태입니다. Chapter 34의 기준대로 게임 스테이트에 둡니다.

```cpp
// MyGameState.h 의 클래스 안
public:
	void SetWinnerName(const FString& NewWinnerName);

	UFUNCTION(BlueprintPure, Category = Match)
	FString GetWinnerName() const { return WinnerName; }

private:
	UFUNCTION()
	void OnRep_WinnerName();

	void ShowResultOnLocalPlayer();

	UPROPERTY(ReplicatedUsing = OnRep_WinnerName)
	FString WinnerName;
```

```cpp
// MyGameState.cpp
#include "MyPlayerController.h"

// GetLifetimeReplicatedProps 안에 추가
DOREPLIFETIME(AMyGameState, WinnerName);

void AMyGameState::SetWinnerName(const FString& NewWinnerName)
{
	WinnerName = NewWinnerName;
	ShowResultOnLocalPlayer();
}

void AMyGameState::OnRep_WinnerName()
{
	ShowResultOnLocalPlayer();
}

void AMyGameState::ShowResultOnLocalPlayer()
{
	AMyPlayerController* PlayerController = Cast<AMyPlayerController>(GetWorld()->GetFirstPlayerController());
	if (PlayerController != nullptr && PlayerController->IsLocalController())
	{
		PlayerController->ShowResult();
	}
}
```

**코드 해석**

- `WinnerName`이 비어 있으면 경기 중이고, 이름이 들어오면 경기가 끝난 것입니다.
- `SetWinnerName`은 서버의 게임모드가 호출합니다. Chapter 32에서 배운 대로 RepNotify 함수는 클라이언트에서만 자동으로 호출되므로, 호스트의 화면을 위해 서버에서도 `ShowResultOnLocalPlayer`를 직접 호출합니다.
- `OnRep_WinnerName`은 값이 도착한 각 클라이언트에서 호출됩니다.
- `GetFirstPlayerController()`는 이 컴퓨터에 있는 첫 번째 플레이어 컨트롤러입니다. 클라이언트에는 자기 컨트롤러 하나뿐이고, 호스트에서는 호스트 자신의 컨트롤러가 가장 먼저 만들어집니다. `IsLocalController()`로 한 번 더 확인합니다.

결과 위젯을 띄우는 일은 플레이어 컨트롤러로 옮깁니다. Chapter 29에서 게임모드에 두었던 `ResultWidgetClass`와 위젯 생성 코드는 지웁니다.

```cpp
// MyPlayerController.h 의 클래스 안
public:
	void ShowResult();

private:
	UPROPERTY(EditDefaultsOnly, Category = UI)
	TSubclassOf<class UUserWidget> ResultWidgetClass;
```

```cpp
// MyPlayerController.cpp
void AMyPlayerController::ShowResult()
{
	if (ResultWidgetClass == nullptr)
	{
		return;
	}

	UUserWidget* ResultWidget = CreateWidget<UUserWidget>(this, ResultWidgetClass);
	if (ResultWidget != nullptr)
	{
		ResultWidget->AddToViewport();
		SetShowMouseCursor(true);
		SetInputMode(FInputModeUIOnly());
	}
}
```

빌드한 뒤 `BP_MyPlayerController`의 **Result Widget Class**에 `WBP_Result`를 지정합니다. `WBP_Result`의 결과 문구 텍스트에는 Chapter 36과 같은 방법으로 바인딩을 만들어, `Get Game State`를 `MyGameState`로 캐스트한 뒤 `Get Winner Name`을 연결합니다.

### 37-4 게임모드에서 경기 끝내기

경기를 끝내는 길은 두 가지입니다. 누군가 목표 점수에 도달하거나, Chapter 34의 남은 시간이 다 되는 것입니다. 두 길이 같은 함수를 부르게 만듭니다.

```cpp
// MyGameMode.h 의 클래스 안
private:
	void FinishMatch(const FString& WinnerName);
	FString FindTopPlayerName() const;

	bool bMatchFinished = false;
```

```cpp
// MyGameMode.cpp
void AMyGameMode::FinishMatch(const FString& WinnerName)
{
	if (bMatchFinished)
	{
		return;
	}

	bMatchFinished = true;
	GetWorldTimerManager().ClearTimer(MatchTimerHandle);

	AMyGameState* MyGameState = GetGameState<AMyGameState>();
	if (MyGameState != nullptr)
	{
		MyGameState->SetWinnerName(WinnerName);
	}
}

FString AMyGameMode::FindTopPlayerName() const
{
	FString TopName = TEXT("Draw");
	int32 TopScore = 0;

	for (APlayerState* PlayerState : GameState->PlayerArray)
	{
		const AMyPlayerState* MyPlayerState = Cast<AMyPlayerState>(PlayerState);
		if (MyPlayerState != nullptr && MyPlayerState->GetKillScore() > TopScore)
		{
			TopScore = MyPlayerState->GetKillScore();
			TopName = MyPlayerState->GetPlayerName();
		}
	}

	return TopName;
}
```

두 곳에서 호출합니다.

```cpp
// OnCharacterDead 함수 — 맨 앞에 추가
if (bMatchFinished)
{
	return;
}

// OnCharacterDead 함수 — 목표 점수 도달 부분을 교체
if (KillerState->GetKillScore() >= MaxKillScore)
{
	FinishMatch(KillerState->GetPlayerName());
}

// TickMatchTime 함수 — 시간이 다 됐을 때 로그 대신
if (Remaining <= 0)
{
	FinishMatch(FindTopPlayerName());
}
```

**코드 해석**

- `bMatchFinished`는 경기를 두 번 끝내지 않게 막습니다. 목표 점수 도달과 시간 종료가 거의 동시에 일어나도 먼저 온 쪽만 처리됩니다.
- 경기가 끝난 뒤의 처치는 점수에 넣지 않도록 `OnCharacterDead`의 맨 앞에서 돌려보냅니다.
- `FindTopPlayerName`은 점수가 가장 높은 플레이어의 이름을 찾습니다. 아무도 점수를 내지 못했으면 `Draw`를 돌려줍니다.
- 게임모드는 "끝났다"는 사실을 게임 스테이트에 적기만 합니다. 화면에 무엇을 띄울지는 각 컴퓨터의 플레이어 컨트롤러가 정합니다. 규칙과 표시가 나뉘었습니다.

플레이어 수 2로 실행해 한쪽이 목표 점수를 채우면 두 창 모두에 결과 화면이 뜹니다.

### 37-5 다시 시작하기

Chapter 29의 다시 시작 버튼은 `Open Level`로 레벨을 다시 열었습니다. 멀티플레이에서 클라이언트가 `Open Level`을 호출하면 그 사람만 혼자 새 레벨로 가 버려 서버와의 연결이 끊깁니다. 모두 함께 이동하려면 Chapter 31에서 배운 `ServerTravel`을 서버가 호출해야 합니다.

```cpp
// MyPlayerController.h 의 클래스 안
public:
	UFUNCTION(BlueprintCallable, Category = Match)
	void RequestRestart();
```

```cpp
// MyPlayerController.cpp
void AMyPlayerController::BeginPlay()
{
	Super::BeginPlay();

	if (IsLocalController())
	{
		SetShowMouseCursor(false);
		SetInputMode(FInputModeGameOnly());

		// Chapter 25의 HUD 위젯 생성 코드는 이 아래에 그대로 둔다
	}
}

void AMyPlayerController::RequestRestart()
{
	if (HasAuthority())
	{
		GetWorld()->ServerTravel(TEXT("?Restart"));
	}
}
```

**코드 해석**

- `ServerTravel`의 주소에 `?Restart`를 넘기면 지금 레벨을 처음부터 다시 엽니다. 접속해 있는 클라이언트도 함께 따라옵니다.
- 호스트의 플레이어 컨트롤러는 서버에 있으므로 `HasAuthority()`가 참입니다. 클라이언트가 버튼을 눌러도 아무 일도 일어나지 않습니다.
- 레벨이 다시 열리면 플레이어 컨트롤러가 새로 만들어집니다. 결과 화면에서 바꿔 둔 입력 모드와 마우스 커서를 `BeginPlay`에서 게임용으로 되돌립니다. 이 줄이 없으면 다시 시작한 뒤 캐릭터가 조작되지 않을 수 있습니다.
- 점수와 남은 시간은 따로 되돌리지 않아도 됩니다. 레벨이 다시 열리면서 게임 스테이트와 플레이어 스테이트가 새로 만들어집니다.

`WBP_Result`의 **다시 시작** 버튼에서 `Open Level` 노드를 지우고, `Get Owning Player`를 `MyPlayerController`로 캐스트해 `Request Restart`를 호출합니다. 클라이언트에게는 눌러도 반응 없는 버튼이 보이게 됩니다. `WBP_Result`의 Event Construct에서 `Get Owning Player`의 `Has Authority`를 `Branch`에 연결하고, 거짓일 때 버튼의 `Set Visibility`를 **Collapsed**로 바꿔 호스트에게만 버튼이 보이게 합니다.

로비 레벨은 마우스 커서가 보여야 합니다. Chapter 31에서 나눈 대로 로비 레벨에는 `BP_MyPlayerController`를 쓰지 않는 별도의 게임모드를 지정해 둡니다.

### 37-6 리슨 서버로 한 판 점검하기

빠른 확인은 플레이어 수 2, **리슨 서버로 플레이**로 합니다.

![Chapter 30과 같은 플레이 설정의 플레이어 수·넷 모드와 PIE 창 두 개 배치](./img/37-pie-listen-server-checklist.png)

방 만들기와 참가까지 포함한 전체 흐름은 Chapter 31처럼 넷 모드를 **스탠드얼론으로 플레이**로 두고 창 두 개로 확인합니다. 아래 항목을 **클라이언트 창을 기준으로** 하나씩 확인합니다.

| 번호 | 확인할 것 | 어긋나면 볼 곳 |
|---|---|---|
| 1 | 방을 만들면 게임 레벨로 이동한다 | Chapter 31의 `OnCreateSessionComplete` |
| 2 | 다른 창에서 방이 목록에 보이고 참가된다 | Chapter 31의 검색 조건, Chapter 36의 목록 |
| 3 | 서로의 이동과 달리기가 보인다 | Chapter 35 |
| 4 | 발사와 근접 공격이 양쪽에 보인다 | Chapter 33의 Server RPC와 Multicast |
| 5 | 맞은 쪽의 체력바가 줄어든다 | Chapter 32의 RepNotify |
| 6 | 처치하면 양쪽 점수판이 바뀐다 | Chapter 36의 `KillScore` 복제 |
| 7 | 남은 시간이 양쪽에서 같다 | Chapter 34의 게임 스테이트 |
| 8 | 경기가 끝나면 양쪽에 결과가 뜬다 | 이 장의 `WinnerName` |
| 9 | 호스트가 다시 시작하면 둘 다 새 판으로 간다 | 이 장의 `RequestRestart` |

에디터 안에서 여러 창으로 플레이할 때 `ServerTravel`이 클라이언트를 데려가지 못하는 경우가 있습니다. 9번은 스탠드얼론 창이나 패키징한 실행 파일로 확인하는 것이 확실합니다.

### 37-7 여기서 더 나아가려면

이 과정에서 다루지 않은 주제입니다. 포트폴리오에서 멀티플레이를 더 깊이 보여 주고 싶을 때 이어서 공부합니다.

| 주제 | 무엇을 하는가 |
|---|---|
| 데디케이티드 서버 빌드 | 화면 없는 서버용 프로그램을 따로 만들어 실행한다 |
| 심리스 트래블 | 레벨을 옮길 때 연결을 끊지 않고 플레이어 정보를 들고 간다 |
| 캐릭터 무브먼트 확장 | 대시 같은 특수 이동을 클라이언트에서 예측하게 만든다 |
| 지연 보상 | 지연이 큰 플레이어가 쏜 총을 서버가 과거 위치로 되돌려 판정한다 |
| 관련성과 갱신 빈도 | 멀리 있는 액터의 복제를 줄여 통신량을 아낀다 |

### 37-8 따라하기 — 네트워크 FPS 한 판

1. `MyGameState`에 `WinnerName`을 추가하고 RepNotify로 결과 화면을 띄운다.
2. 결과 위젯 생성 코드를 게임모드에서 플레이어 컨트롤러로 옮긴다.
3. 게임모드에 `FinishMatch`를 만들어 목표 점수 도달과 시간 종료가 모두 이 함수를 부르게 한다.
4. `MatchSeconds`를 `20`으로 줄여 시간 종료로 끝나는 경우를 확인한 뒤 되돌린다.
5. 다시 시작 버튼을 `RequestRestart`에 연결하고 호스트에게만 보이게 한다.
6. 스탠드얼론 창 두 개로 37-6의 표 아홉 항목을 순서대로 통과시킨다.
7. 통과하지 못한 항목은 표의 오른쪽 칸에 적힌 장으로 돌아가 고친다.

이 장으로 Part 04 네트워킹과 과정 전체를 마칩니다.

### 연습문제

1. 문제: 리슨 서버에서 호스트가 게임을 나가면 어떤 일이 생기는지 쓰고, 데디케이티드 서버에서는 왜 그 문제가 없는지 한 문장으로 쓰세요.
2. 문제: 게임모드가 처치한 플레이어의 컨트롤러로 결과 위젯을 직접 만들면 클라이언트 화면에 뜨지 않는 이유를 한 문장으로 쓰세요.
3. 문제: 다시 시작에 `Open Level` 대신 `ServerTravel`을 쓰는 이유를 한 문장으로 쓰세요.
4. 문제: 접속부터 결과 화면까지의 흐름을 다섯 단계 이내의 목록으로 쓰세요.  
   - 조건: 단계마다 담당하는 클래스를 하나씩 적을 것

### 정답 포인트

- 서버가 사라져 방이 끝난다. 데디케이티드 서버는 플레이어와 따로 돌아가는 프로그램이라 누가 나가도 유지된다. 게임모드는 서버에서 실행되므로 위젯이 서버 쪽에서 만들어지고 그 사람의 화면에는 닿지 않는다. `Open Level`은 호출한 컴퓨터만 이동하고, `ServerTravel`은 서버와 접속한 클라이언트가 함께 이동한다. 방 생성과 참가는 게임 인스턴스, 폰 준비는 게임모드, 입력과 요청은 캐릭터, 점수와 결과는 플레이어 스테이트와 게임 스테이트, 화면 표시는 플레이어 컨트롤러.

---

[상위 문서로 돌아가기](./README.md)
