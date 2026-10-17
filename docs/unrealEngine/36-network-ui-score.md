# Chapter 36 네트워크 UI·점수

## 학습 목표

- 세션 요청의 진행 상황과 방 목록을 로비 UI에 표시할 수 있다.
- 처치 점수를 복제하고 내 HUD에 표시할 수 있다.
- 게임 스테이트의 정보로 모든 플레이어의 점수판과 남은 시간을 표시할 수 있다.
- 내 화면에만 필요한 UI와 모두가 같은 값을 보는 UI를 구분해 데이터를 고를 수 있다.

## 본문

### 36-1 UI는 요청하고, 결과는 나중에 온다

Chapter 31의 로비 위젯은 버튼을 눌러도 화면에 아무 변화가 없었습니다. 방이 만들어지는 중인지, 검색이 끝났는지는 출력 로그를 봐야 알 수 있었습니다. 플레이어는 로그를 볼 수 없으므로 화면에 알려 줘야 합니다.

세션 함수는 요청과 완료가 따로였습니다. UI도 같은 구조를 따릅니다.

1. 버튼을 누르면 게임 인스턴스에 요청만 한다.
2. 게임 인스턴스는 완료 델리게이트가 호출되었을 때 "결과가 나왔다"고 방송한다.
3. 위젯은 그 방송을 듣고 글자를 바꾼다.

위젯이 매 프레임 "끝났어?"라고 묻지 않습니다. Chapter 25에서 체력바를 델리게이트로 갱신한 것과 같은 방식입니다.

### 36-2 로비 상태 문구

게임 인스턴스에 블루프린트가 들을 수 있는 델리게이트를 추가합니다.

```cpp
// MyGameInstance.h — UCLASS() 위
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnLobbyStatusChangedSignature, const FString&, Message);

// 클래스 안
public:
	UPROPERTY(BlueprintAssignable, Category = Lobby)
	FOnLobbyStatusChangedSignature OnLobbyStatusChanged;
```

요청할 때와 결과가 나왔을 때 방송합니다. Chapter 31의 함수에 한 줄씩 추가합니다.

```cpp
// MyGameInstance.cpp

// Host 함수의 맨 끝
OnLobbyStatusChanged.Broadcast(TEXT("Creating room..."));

// OnCreateSessionComplete 의 실패 처리 안
OnLobbyStatusChanged.Broadcast(TEXT("Create room failed"));

// RefreshServerList 함수의 맨 끝
OnLobbyStatusChanged.Broadcast(TEXT("Searching..."));

// OnJoinSessionComplete 에서 접속 주소를 얻지 못했을 때
OnLobbyStatusChanged.Broadcast(TEXT("Join failed"));
```

위젯에서 받습니다.

1. `WBP_Lobby`에 텍스트를 하나 추가하고 이름을 `StatusText`로 바꾼 뒤 **변수 여부**에 체크한다.
2. 그래프 탭의 **Event Construct**에서 `Get Game Instance`를 `MyGameInstance`로 캐스트한다.
3. 캐스트 결과에서 `Bind Event to On Lobby Status Changed` 노드를 만든다.
4. **Event** 핀을 드래그해 커스텀 이벤트를 추가한다. `Message` 핀이 달린 이벤트가 만들어진다.
5. 커스텀 이벤트에서 `StatusText`의 `SetText (Text)`에 `Message`를 연결한다.

빌드하고 스탠드얼론 창 두 개로 실행합니다. 버튼을 누르면 문구가 바뀌고, 방을 만드는 데 실패하면 실패 문구가 남습니다. 코드의 문구는 영문으로 적었습니다. 소스 파일의 저장 방식에 따라 한글 문자열이 깨질 수 있기 때문입니다. 한글로 보여 주고 싶다면 위젯 쪽에서 문구를 바꿔 표시합니다.

### 36-3 방 목록 보여 주기

검색 결과도 화면에 보여 줍니다. Chapter 31에서 로그로 찍던 줄을 글자로 모아 둡니다.

```cpp
// MyGameInstance.h 의 클래스 안
public:
	UFUNCTION(BlueprintPure, Category = Lobby)
	FString GetServerListText() const { return ServerListText; }

private:
	FString ServerListText;
```

```cpp
// MyGameInstance.cpp
void UMyGameInstance::OnFindSessionsComplete(bool bSuccess)
{
	ServerListText.Empty();

	if (!bSuccess || !SessionSearch.IsValid())
	{
		OnLobbyStatusChanged.Broadcast(TEXT("Search failed"));
		return;
	}

	for (int32 Index = 0; Index < SessionSearch->SearchResults.Num(); ++Index)
	{
		const FOnlineSessionSearchResult& Result = SessionSearch->SearchResults[Index];

		FString ServerName;
		Result.Session.SessionSettings.Get(FName(TEXT("ServerName")), ServerName);

		const int32 MaxPlayers = Result.Session.SessionSettings.NumPublicConnections;
		const int32 CurrentPlayers = MaxPlayers - Result.Session.NumOpenPublicConnections;

		ServerListText += FString::Printf(TEXT("[%d] %s (%d/%d)\n"),
			Index, *ServerName, CurrentPlayers, MaxPlayers);
	}

	OnLobbyStatusChanged.Broadcast(
		FString::Printf(TEXT("Found %d room(s)"), SessionSearch->SearchResults.Num()));
}
```

**코드 해석**

- `ServerListText`는 방 한 개를 한 줄로 적어 이어 붙인 글자입니다. `+=`로 `FString` 뒤에 글자를 덧붙입니다.
- `Empty()`는 이전 검색의 결과를 비웁니다.
- 목록을 다 만든 뒤에 방송하므로, 방송을 들은 위젯이 `GetServerListText`를 읽으면 항상 완성된 목록을 얻습니다.

위젯에 목록과 방 번호 입력을 붙입니다.

1. `WBP_Lobby`에 텍스트 `ServerListText`를 추가하고 변수 여부에 체크한다.
2. 36-2에서 만든 커스텀 이벤트의 끝에 `Get Server List Text`를 `ServerListText`의 `SetText (Text)`로 연결한다.
3. 팔레트에서 **스핀 박스**를 추가하고 이름을 `RoomIndex`로 바꾼다. 디테일 패널에서 **최솟값**을 `0`, **델타**를 `1`로 정한다.
4. **참가** 버튼의 On Clicked에서 `Join`의 **Index**에 넣었던 `0` 대신, `RoomIndex`의 `Get Value`를 `Truncate` 노드로 정수로 바꿔 연결한다.

**스핀 박스**는 숫자를 입력하거나 끌어서 바꾸는 위젯입니다. 값이 소수라서 `Truncate`로 소수점 아래를 버려 정수로 만듭니다.

![로비 접속 UI 플레이 화면: 세션 목록과 참가 버튼이 보이는 예시](./img/36-lobby-play-screen.png)

목록의 줄마다 버튼을 따로 만들면 번호를 입력하지 않아도 됩니다. 그 방법은 줄 하나를 별도의 위젯 블루프린트로 만들고 검색 결과 수만큼 생성해 세로 박스에 넣는 것으로, 이 장의 따라하기를 마친 뒤 도전해 봅니다.

### 36-4 처치 점수 복제하기

게임 레벨로 넘어갑니다. Chapter 22에서 만든 `KillScore`는 아직 복제되지 않습니다. 서버에서만 값이 오르므로 클라이언트의 화면에는 계속 `0`으로 보입니다. Chapter 30의 방법으로 복제를 켭니다.

```cpp
// MyPlayerState.h
public:
	virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;

private:
	UPROPERTY(Replicated, VisibleInstanceOnly, BlueprintReadOnly, Category = Score, meta = (AllowPrivateAccess = "true"))
	int32 KillScore = 0;
```

```cpp
// MyPlayerState.cpp
#include "Net/UnrealNetwork.h"

void AMyPlayerState::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);
	DOREPLIFETIME(AMyPlayerState, KillScore);
}
```

플레이어 스테이트 액터는 처음부터 복제가 켜져 있으므로 변수만 등록하면 됩니다. 점수를 올리는 `AddKillScore`는 게임모드의 `OnCharacterDead`에서만 호출되고, 게임모드는 서버에만 있으므로 가드는 필요 없습니다.

### 36-5 내 점수를 HUD에 표시하기

Chapter 25의 `WBP_HealthBar`에 점수 텍스트를 추가합니다.

1. `WBP_HealthBar`에 텍스트를 추가하고 이름을 `ScoreText`로 바꾼다.
2. 디테일 패널의 **텍스트** 옆 **바인드** 드롭다운에서 **바인딩 생성**을 고른다.
3. 만들어진 함수 안에서 `Get Owning Player`의 `Player State`를 `MyPlayerState`로 캐스트한다.
4. 캐스트 결과에서 `Kill Score`를 꺼내 **Return Value**에 연결한다. 숫자를 글자로 바꾸는 변환 노드가 자동으로 끼어든다.
5. 캐스트가 실패했을 때는 `0`을 돌려주는 Return Node를 따로 연결한다.

체력바는 델리게이트로 갱신했는데 점수는 25-7의 바인딩을 쓴 데에는 이유가 있습니다. 클라이언트에서는 위젯이 만들어지는 순간에 내 플레이어 스테이트가 아직 서버에서 도착하지 않았을 수 있습니다. Event Construct에서 플레이어 스테이트의 델리게이트에 등록하려 하면 캐스트가 실패해 등록되지 않고, 그 뒤로는 다시 시도하지 않습니다. 바인딩은 화면을 그릴 때마다 다시 읽으므로 플레이어 스테이트가 늦게 도착해도 그때부터 값이 나옵니다.

복제되는 액터는 서버에서 만들어진 뒤 조금 늦게 클라이언트에 나타납니다. 클라이언트에서 "시작하자마자 한 번" 가져오는 코드는 이 시간 차이 때문에 실패할 수 있다는 점을 기억해 둡니다.

클라이언트 창에서 체력바가 움직이지 않는다면 같은 원인입니다. 위젯이 만들어질 때 내 폰이 아직 도착하지 않아 Event Construct의 등록이 실패한 것입니다. 이때는 `HealthComponent.h`의 `GetHealthPercent` 위에 `UFUNCTION(BlueprintPure, Category = Health)`를 붙이고, 체력바의 퍼센트도 바인딩으로 바꿔 이 함수를 읽게 합니다.

### 36-6 모두의 점수판

내 점수뿐 아니라 모든 플레이어의 점수를 보여 주는 점수판을 만듭니다. Chapter 34의 표에서 본 대로 다른 사람의 컨트롤러는 내 컴퓨터에 없지만, 플레이어 스테이트는 모두 있습니다. 게임 스테이트의 `PlayerArray`에 그 목록이 들어 있습니다.

Chapter 34에서 만든 `MyGameState`에 목록을 글자로 만들어 주는 함수를 추가합니다.

```cpp
// MyGameState.h 의 클래스 안
public:
	UFUNCTION(BlueprintPure, Category = Score)
	FString GetScoreboardText() const;
```

```cpp
// MyGameState.cpp
#include "MyPlayerState.h"

FString AMyGameState::GetScoreboardText() const
{
	FString Result;

	for (APlayerState* PlayerState : PlayerArray)
	{
		const AMyPlayerState* MyPlayerState = Cast<AMyPlayerState>(PlayerState);
		if (MyPlayerState != nullptr)
		{
			Result += FString::Printf(TEXT("%s : %d\n"),
				*MyPlayerState->GetPlayerName(), MyPlayerState->GetKillScore());
		}
	}

	return Result;
}
```

**코드 해석**

- `PlayerArray`는 Chapter 22의 게임모드에서 쓴 것과 같은 배열입니다. 게임 스테이트가 복제되므로 클라이언트에서도 읽을 수 있습니다.
- `GetPlayerName()`은 플레이어 스테이트에 들어 있는 플레이어 이름입니다. 스팀 서브시스템에서는 스팀 닉네임이 들어옵니다.

위젯에 붙입니다.

1. `WBP_HealthBar`의 오른쪽 위에 텍스트 `ScoreboardText`를 추가하고 앵커를 오른쪽 위로 정한다.
2. 텍스트에 바인딩을 생성하고, 함수 안에서 `Get Game State`를 `MyGameState`로 캐스트해 `Get Scoreboard Text`를 Return Value에 연결한다.
3. 화면 위쪽 가운데에 텍스트 `TimeText`를 추가하고 같은 방법으로 `Get Remaining Seconds`를 연결한다.

![게임 HUD 체력바·점수와 별도 스코어보드 위젯이 함께 보이는 플레이 화면](./img/36-hud-scoreboard.png)

플레이어 수 2, 리슨 서버로 실행해 한쪽이 적을 처치하면 두 창의 점수판이 함께 바뀝니다.

### 36-7 로컬 UI와 공유 UI

지금까지 만든 UI를 데이터가 어디서 오는지로 나누면 두 종류입니다.

| UI | 데이터가 있는 곳 | 예 |
|---|---|---|
| 로컬 UI | 내 폰, 내 플레이어 스테이트 | 내 체력바, 내 점수 |
| 공유 UI | 게임 스테이트, 모든 플레이어 스테이트 | 점수판, 남은 시간 |

UI를 새로 만들 때는 "이 값을 누가 알아야 하는가"를 먼저 정하고, Chapter 34의 표에서 그 값이 있어야 할 클래스를 고른 다음, 위젯이 그 클래스에서 읽게 합니다. 위젯이 게임모드에서 직접 읽게 만들면 호스트 화면에서만 동작합니다.

위젯을 띄우는 코드에는 Chapter 25의 `IsLocalController()` 확인을 그대로 둡니다. 서버에는 모든 플레이어의 컨트롤러가 있으므로, 이 확인이 없으면 호스트 화면에 다른 사람 몫의 HUD까지 겹쳐 뜹니다.

### 36-8 따라하기 — 로비와 점수 UI

1. 게임 인스턴스에 `OnLobbyStatusChanged`를 추가하고 로비 위젯의 `StatusText`에 연결한다.
2. 검색 결과를 `ServerListText`로 모아 로비 위젯에 표시한다.
3. 스핀 박스로 방 번호를 골라 참가하게 한다.
4. `KillScore`를 복제하고 `WBP_HealthBar`에 내 점수를 표시한다.
5. `GetScoreboardText`를 만들어 점수판을 표시한다.
6. 남은 시간을 표시한다.
7. 플레이어 수 2로 실행해 한쪽이 적을 처치했을 때 두 창의 점수판이 같은 값으로 바뀌는지 확인한다.
8. 내 체력바는 내 체력만, 점수판은 두 사람의 점수를 모두 보여 주는지 확인한다.

### 연습문제

1. 문제: 로비 위젯이 매 프레임 세션 상태를 확인하지 않고 델리게이트를 쓰는 이유를 한 문장으로 쓰세요.
2. 문제: 클라이언트의 위젯이 Event Construct에서 내 플레이어 스테이트를 가져오려 할 때 실패할 수 있는 이유를 한 문장으로 쓰세요.
3. 문제: 점수판 데이터를 게임모드에만 두면 생기는 문제를 한 문장으로 쓰세요.
4. 문제: 내 체력바와 점수판이 데이터를 읽는 곳의 차이를 한 문장으로 쓰세요.

### 정답 포인트

- 결과는 요청한 뒤 언젠가 한 번 오므로 그 순간에만 갱신하면 된다. 복제되는 액터는 서버에서 만들어진 뒤 조금 늦게 클라이언트에 도착한다. 게임모드는 클라이언트에 없어서 클라이언트 화면에 점수판을 그릴 수 없다. 체력바는 내 폰의 값을, 점수판은 게임 스테이트에 모인 모든 플레이어 스테이트의 값을 읽는다.

---

[상위 문서로 돌아가기](./README.md)
