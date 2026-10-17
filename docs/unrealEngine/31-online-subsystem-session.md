# Chapter 31 온라인 서브시스템과 세션

## 학습 목표

- 온라인 서브시스템이 왜 필요한지 설명할 수 있다.
- 게임 인스턴스의 역할을 설명하고 세션 인터페이스를 준비할 수 있다.
- 세션 생성·검색·참가·소멸의 호출 순서와 완료 델리게이트 처리를 구현할 수 있다.
- 스팀 온라인 서브시스템을 켜는 설정 항목을 설명할 수 있다.

## 본문

### 31-1 온라인 서비스와 온라인 서브시스템

Chapter 30에서는 에디터가 창 두 개를 자동으로 연결해 주었습니다. 실제로는 다른 집에 있는 친구가 내가 만든 방을 찾아 들어와야 합니다. 방 목록을 보관하고, 친구를 초대하고, 서로의 컴퓨터를 연결해 주는 일은 인터넷 어딘가에 있는 **온라인 서비스**가 합니다.

온라인 서비스는 여러 회사가 제공합니다. 스팀, 에픽 온라인 서비스, 콘솔 회사들의 서비스가 있고, 서비스마다 쓰는 방법이 다릅니다. 게임을 여러 플랫폼에 내려면 서비스마다 코드를 따로 써야 하는 문제가 생깁니다.

**온라인 서브시스템**은 서로 다른 온라인 서비스를 같은 방법으로 쓸 수 있게 감싸 둔 언리얼의 공통 창구입니다. 코드는 온라인 서브시스템의 함수만 호출하고, 실제로 어느 서비스가 쓰일지는 설정 파일에서 고릅니다.

기능은 **인터페이스** 단위로 나뉘어 있습니다.

| 인터페이스 | 담당 |
|---|---|
| `IOnlineIdentity` | 로그인과 계정 정보 |
| `IOnlineSession` | 방 만들기, 검색, 참가, 종료 |
| `IOnlineFriends` | 친구 목록 |
| `IOnlineAchievements` | 업적 |
| `IOnlineLeaderboards` | 순위표 |

아무 서비스도 설정하지 않으면 `NULL`이라는 기본 서브시스템이 쓰입니다. 인터넷 서비스 없이 같은 공유기에 연결된 컴퓨터끼리만 찾을 수 있습니다. 이런 근거리 연결을 **LAN**이라고 합니다. 이 장의 실습은 `NULL` 서브시스템으로 먼저 성공시킨 뒤 스팀으로 바꾸는 순서로 진행합니다.

공통 창구가 있어도 서비스마다 세부 동작은 조금씩 다릅니다. 문제가 생겼을 때는 쓰고 있는 서비스의 특성을 따로 확인해야 합니다.

### 31-2 세션

함께 플레이할 사람들이 모이는 방 하나를 **세션**이라고 합니다. 세션을 다루는 순서는 다음과 같습니다.

| 단계 | 누가 | 하는 일 |
|---|---|---|
| 생성 | 호스트 | 방 이름, 최대 인원, 공개 여부를 정해 방을 만든다 |
| 검색 | 참가자 | 만들어진 방 목록을 요청한다 |
| 참가 | 참가자 | 목록에서 방을 골라 들어간다 |
| 소멸 | 호스트 | 방을 없앤다 |

세션 함수는 호출하자마자 결과가 나오지 않습니다. 인터넷 너머의 서비스에 요청을 보내고 답을 기다려야 하기 때문입니다. 그래서 요청 함수와 **완료 델리게이트**가 짝을 이룹니다.

| 요청 함수 | 완료 델리게이트 |
|---|---|
| `CreateSession` | `OnCreateSessionComplete` |
| `FindSessions` | `OnFindSessionsComplete` |
| `JoinSession` | `OnJoinSessionComplete` |
| `DestroySession` | `OnDestroySessionComplete` |

요청을 보낸 뒤 게임은 계속 진행되고, 답이 오면 델리게이트에 등록해 둔 함수가 호출됩니다.

### 31-3 게임 인스턴스

세션 코드는 어디에 두어야 할까요? 게임모드와 캐릭터는 레벨이 바뀌면 사라지고 새로 만들어집니다. 방을 만든 뒤 로비 레벨에서 게임 레벨로 이동하면 세션 정보를 들고 있던 객체가 없어져 버립니다.

**게임 인스턴스**는 게임 프로그램이 켜질 때 만들어져 꺼질 때까지 유지되는 객체입니다. 레벨이 바뀌어도 사라지지 않습니다.

| 역할 | 예 |
|---|---|
| 레벨이 바뀌어도 남아야 하는 데이터 보관 | 플레이어 프로필, 옵션 값 |
| 네트워크 초기화와 세션 관리 | 온라인 서브시스템 준비 |
| 프로그램 시작과 종료 처리 | `Init`, `Shutdown` |

먼저 `Practice.Build.cs`의 모듈 목록에 `"OnlineSubsystem"`을 추가하고, **툴 → 새 C++ 클래스**로 부모가 `GameInstance`인 `MyGameInstance`를 만듭니다.

```cpp
// MyGameInstance.h
#include "Interfaces/OnlineSessionInterface.h"

// 클래스 안
public:
	virtual void Init() override;

	UFUNCTION(BlueprintCallable)
	void Host();

	UFUNCTION(BlueprintCallable)
	void RefreshServerList();

	UFUNCTION(BlueprintCallable)
	void Join(int32 Index);

private:
	void CreateSession();

	void OnCreateSessionComplete(FName SessionName, bool bSuccess);
	void OnDestroySessionComplete(FName SessionName, bool bSuccess);
	void OnFindSessionsComplete(bool bSuccess);
	void OnJoinSessionComplete(FName SessionName, EOnJoinSessionCompleteResult::Type Result);

	IOnlineSessionPtr SessionInterface;
	TSharedPtr<class FOnlineSessionSearch> SessionSearch;
```

```cpp
// MyGameInstance.cpp
#include "MyGameInstance.h"
#include "Practice.h"
#include "OnlineSubsystem.h"
#include "OnlineSessionSettings.h"

void UMyGameInstance::Init()
{
	Super::Init();

	IOnlineSubsystem* Subsystem = IOnlineSubsystem::Get();
	if (Subsystem == nullptr)
	{
		return;
	}

	PLOG(Warning, TEXT("Subsystem : %s"), *Subsystem->GetSubsystemName().ToString());

	SessionInterface = Subsystem->GetSessionInterface();
	if (SessionInterface.IsValid())
	{
		SessionInterface->OnCreateSessionCompleteDelegates.AddUObject(this, &UMyGameInstance::OnCreateSessionComplete);
		SessionInterface->OnDestroySessionCompleteDelegates.AddUObject(this, &UMyGameInstance::OnDestroySessionComplete);
		SessionInterface->OnFindSessionsCompleteDelegates.AddUObject(this, &UMyGameInstance::OnFindSessionsComplete);
		SessionInterface->OnJoinSessionCompleteDelegates.AddUObject(this, &UMyGameInstance::OnJoinSessionComplete);
	}
}
```

**코드 해석**

- `Init`은 프로그램이 실행되자마자 한 번 호출됩니다.
- `IOnlineSubsystem::Get()`은 설정에서 고른 온라인 서브시스템을 가져옵니다. 로그로 이름을 찍어 `NULL`인지 `Steam`인지 확인합니다.
- `GetSessionInterface()`로 세션 인터페이스를 얻습니다. `IOnlineSessionPtr`은 이 인터페이스를 가리키는 스마트 포인터 타입이고, `IsValid()`로 비어 있지 않은지 확인합니다.
- 네 개의 완료 델리게이트에 함수를 미리 등록해 둡니다. Chapter 21에서 쓴 `AddUObject`입니다.

빌드한 뒤 **편집 → 프로젝트 세팅 → 맵 & 모드 → 게임 인스턴스 클래스**를 `MyGameInstance`로 바꿉니다.

### 31-4 세션 생성

```cpp
void UMyGameInstance::Host()
{
	if (!SessionInterface.IsValid())
	{
		return;
	}

	if (SessionInterface->GetNamedSession(NAME_GameSession) != nullptr)
	{
		SessionInterface->DestroySession(NAME_GameSession);
	}
	else
	{
		CreateSession();
	}
}

void UMyGameInstance::CreateSession()
{
	FOnlineSessionSettings Settings;
	Settings.bIsLANMatch = (IOnlineSubsystem::Get()->GetSubsystemName() == TEXT("NULL"));
	Settings.NumPublicConnections = 4;
	Settings.bShouldAdvertise = true;
	Settings.bUsesPresence = true;
	Settings.bUseLobbiesIfAvailable = true;
	Settings.Set(FName(TEXT("ServerName")), FString(TEXT("My Room")),
		EOnlineDataAdvertisementType::ViaOnlineServiceAndPing);

	SessionInterface->CreateSession(0, NAME_GameSession, Settings);
}

void UMyGameInstance::OnDestroySessionComplete(FName SessionName, bool bSuccess)
{
	if (bSuccess)
	{
		CreateSession();
	}
}

void UMyGameInstance::OnCreateSessionComplete(FName SessionName, bool bSuccess)
{
	if (!bSuccess)
	{
		PLOG(Error, TEXT("CreateSession failed"));
		return;
	}

	GetWorld()->ServerTravel(TEXT("/Game/Maps/GameMap?listen"));
}
```

**코드 해석**

`FOnlineSessionSettings`가 방의 설정입니다.

| 변수 | 의미 |
|---|---|
| `bIsLANMatch` | 근거리 연결용 방인가. `NULL` 서브시스템일 때만 참으로 둔다 |
| `NumPublicConnections` | 들어올 수 있는 최대 인원 |
| `bShouldAdvertise` | 검색 목록에 공개할 것인가 |
| `bUsesPresence` | 친구에게 내가 이 방에 있다는 것을 보여 줄 것인가 |
| `bUseLobbiesIfAvailable` | 서비스가 로비 기능을 지원하면 로비로 만들 것인가 |

- `Settings.Set(키, 값, 공개 범위)`는 방에 직접 정한 정보를 붙입니다. 여기서는 `ServerName`이라는 키로 방 이름을 붙였습니다. `ViaOnlineServiceAndPing`은 온라인 서비스 검색과 근거리 검색 양쪽에 모두 공개한다는 뜻입니다.
- `CreateSession(플레이어 번호, 세션 이름, 설정)`으로 생성을 요청합니다. `NAME_GameSession`은 엔진이 정해 둔 기본 세션 이름입니다.
- 이미 만든 세션이 남아 있으면 같은 이름으로 또 만들 수 없습니다. `Host`는 먼저 `GetNamedSession`으로 확인해, 있으면 없앤 뒤 완료 델리게이트에서 다시 만듭니다.
- `ServerTravel(레벨 주소)`는 서버가 레벨을 옮기는 함수입니다. 접속해 있는 클라이언트도 함께 따라옵니다. 주소 끝의 `?listen`은 그 레벨을 리슨 서버로 열어 다른 사람이 접속할 수 있게 하라는 옵션입니다.

레벨 주소 `/Game/Maps/GameMap`은 자신의 게임 레벨 경로로 바꿉니다.

### 31-5 세션 검색

```cpp
void UMyGameInstance::RefreshServerList()
{
	if (!SessionInterface.IsValid())
	{
		return;
	}

	SessionSearch = MakeShareable(new FOnlineSessionSearch());
	SessionSearch->bIsLanQuery = (IOnlineSubsystem::Get()->GetSubsystemName() == TEXT("NULL"));
	SessionSearch->MaxSearchResults = 100;
	SessionSearch->QuerySettings.Set(SEARCH_LOBBIES, true, EOnlineComparisonOp::Equals);

	SessionInterface->FindSessions(0, SessionSearch.ToSharedRef());
}

void UMyGameInstance::OnFindSessionsComplete(bool bSuccess)
{
	if (!bSuccess || !SessionSearch.IsValid())
	{
		return;
	}

	for (int32 Index = 0; Index < SessionSearch->SearchResults.Num(); ++Index)
	{
		const FOnlineSessionSearchResult& Result = SessionSearch->SearchResults[Index];

		FString ServerName;
		Result.Session.SessionSettings.Get(FName(TEXT("ServerName")), ServerName);

		const int32 MaxPlayers = Result.Session.SessionSettings.NumPublicConnections;
		const int32 CurrentPlayers = MaxPlayers - Result.Session.NumOpenPublicConnections;

		PLOG(Warning, TEXT("[%d] %s (%d/%d) host: %s"),
			Index, *ServerName, CurrentPlayers, MaxPlayers, *Result.Session.OwningUserName);
	}
}
```

**코드 해석**

- `FOnlineSessionSearch`는 검색 조건을 담고, 검색이 끝나면 결과도 여기에 담깁니다.
- `MakeShareable(new ...)`는 새로 만든 객체를 `TSharedPtr`에 넣어 줍니다. `TSharedPtr`은 쓰는 곳이 없어지면 스스로 메모리를 정리하는 스마트 포인터입니다. `FOnlineSessionSearch`는 언리얼 객체가 아니어서 가비지 컬렉션 대상이 아니므로 이 방식으로 관리합니다.
- `QuerySettings.Set`은 검색 조건을 추가합니다. 31-4에서 로비로 만들었으므로 로비를 검색합니다.
- `FindSessions(플레이어 번호, 검색 객체)`로 검색을 요청합니다.
- 완료되면 `SearchResults` 배열에 결과가 들어 있습니다. 31-4에서 붙인 `ServerName`을 `Get`으로 꺼냅니다.
- `NumOpenPublicConnections`는 남은 자리 수입니다. 최대 인원에서 빼면 현재 인원이 됩니다.

실제 게임에서는 이 정보를 로그 대신 방 목록 UI에 표시합니다.

### 31-6 세션 참가

```cpp
void UMyGameInstance::Join(int32 Index)
{
	if (!SessionInterface.IsValid() || !SessionSearch.IsValid())
	{
		return;
	}

	if (!SessionSearch->SearchResults.IsValidIndex(Index))
	{
		return;
	}

	SessionInterface->JoinSession(0, NAME_GameSession, SessionSearch->SearchResults[Index]);
}

void UMyGameInstance::OnJoinSessionComplete(FName SessionName, EOnJoinSessionCompleteResult::Type Result)
{
	if (!SessionInterface.IsValid())
	{
		return;
	}

	FString Address;
	if (!SessionInterface->GetResolvedConnectString(SessionName, Address))
	{
		PLOG(Error, TEXT("Could not get connect string"));
		return;
	}

	APlayerController* PlayerController = GetFirstLocalPlayerController();
	if (PlayerController != nullptr)
	{
		PlayerController->ClientTravel(Address, ETravelType::TRAVEL_Absolute);
	}
}
```

**코드 해석**

- `JoinSession(플레이어 번호, 세션 이름, 검색 결과)`로 참가를 요청합니다. 두 번째 값은 참가한 세션을 내 컴퓨터에서 부를 이름입니다. 이후 이 세션을 다룰 때 같은 이름을 씁니다.
- `IsValidIndex`로 목록 범위를 벗어난 번호를 걸러 냅니다.
- 참가가 완료되어도 아직 호스트의 레벨에 들어간 것은 아닙니다. `GetResolvedConnectString`으로 호스트의 접속 주소를 받아 옵니다.
- `ClientTravel(주소, 이동 방식)`은 클라이언트 한 명이 그 주소의 서버로 이동하는 함수입니다.

| 함수 | 누가 호출 | 누가 이동 |
|---|---|---|
| `ServerTravel` | 서버 | 서버와 접속한 모든 클라이언트 |
| `ClientTravel` | 클라이언트 | 그 클라이언트 혼자 |

### 31-7 로비 UI에 연결

세 함수에 `UFUNCTION(BlueprintCallable)`을 붙였으므로 위젯 블루프린트의 버튼에서 호출할 수 있습니다.

1. 로비용 레벨 `LobbyMap`과 게임용 레벨 `GameMap`을 나눈다.
2. 위젯 블루프린트 `WBP_Lobby`에 **방 만들기**, **새로고침**, **참가** 버튼을 놓는다.
3. 각 버튼의 **On Clicked**에서 `Get Game Instance`를 `MyGameInstance`로 캐스트하고 `Host`, `Refresh Server List`, `Join`을 호출한다. `Join`의 **Index**에는 우선 `0`을 넣는다.
4. `LobbyMap`의 레벨 블루프린트에서 `WBP_Lobby`를 화면에 올리고 마우스 커서를 보이게 한다.

![로비 위젯 디자이너 화면: 방 만들기·새로고침·참가 버튼이 세로로 배치된 모습](./img/31-lobby-session-ui.png)

대기 공간과 실제 플레이 공간을 다른 레벨로 나누면 게임모드와 규칙을 각각 다르게 줄 수 있습니다.

테스트는 에디터의 **스탠드얼론 게임**으로 창을 두 개 띄워서 합니다. 플레이 설정에서 넷 모드를 **스탠드얼론으로 플레이**, 플레이어 수를 `2`로 두면 서로 연결되지 않은 창 두 개가 뜹니다. 한 창에서 방을 만들고 다른 창에서 새로고침한 뒤 참가합니다.

### 31-8 스팀 온라인 서브시스템 켜기

여기까지는 `NULL` 서브시스템이라 같은 공유기 안에서만 방이 보입니다. 스팀으로 바꾸면 인터넷으로 방을 찾고 스팀 친구를 초대할 수 있습니다. 코드는 그대로 두고 설정만 바꿉니다.

1. **편집 → 플러그인**에서 **Online Subsystem Steam**을 켜고 에디터를 다시 시작한다.
2. `Practice.Build.cs`의 모듈 목록에 `"OnlineSubsystemSteam"`을 추가한다.
3. `Config/DefaultEngine.ini`에 아래 내용을 추가한다.

```ini
[/Script/Engine.GameEngine]
+NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="OnlineSubsystemSteam.SteamNetDriver",DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")

[OnlineSubsystem]
DefaultPlatformService=Steam

[OnlineSubsystemSteam]
bEnabled=true
SteamDevAppId=480

[/Script/OnlineSubsystemSteam.SteamNetDriver]
NetConnectionClassName="OnlineSubsystemSteam.SteamNetConnection"
```

| 항목 | 의미 |
|---|---|
| `DefaultPlatformService` | 사용할 온라인 서비스. 지정하지 않으면 `NULL` |
| `SteamDevAppId` | 스팀이 게임마다 부여하는 번호 |

`480`은 스팀이 개발 테스트용으로 열어 둔 번호입니다. 실제로 출시할 게임은 스팀의 개발자 사이트인 스팀웍스에 게임을 등록하고 받은 번호로 바꿔야 합니다. 에픽 온라인 서비스를 쓴다면 해당 플러그인을 켜고 `DefaultPlatformService`를 그 서비스 이름으로 바꾸는 식으로 같은 흐름을 따릅니다.

스팀 서브시스템은 에디터 안에서 플레이할 때는 동작하지 않습니다. 스팀 프로그램을 켜고 로그인한 상태에서 **스탠드얼론 게임**으로 실행하거나 패키징한 실행 파일로 테스트합니다. 화면에 스팀 오버레이 알림이 뜨고 31-3의 로그에 `Steam`이 찍히면 성공입니다. 스팀 계정 하나로는 한 컴퓨터에서 방을 만들고 스스로 참가하는 테스트를 할 수 없으므로, 컴퓨터 두 대와 계정 두 개가 필요합니다.

### 31-9 따라하기 — 세션 생성과 참가

1. `MyGameInstance`를 만들어 프로젝트 세팅에 지정하고, 실행 시 서브시스템 이름 로그를 확인한다.
2. `Host`와 `CreateSession`을 구현해 방을 만들면 게임 레벨로 이동하는지 확인한다.
3. `RefreshServerList`를 구현해 다른 창에서 방 목록이 로그에 찍히는지 확인한다.
4. `Join`을 구현해 참가한 창이 호스트의 레벨로 이동하는지 확인한다.
5. 로비 위젯의 버튼에 세 함수를 연결한다.
6. 호스트가 방을 두 번 연속으로 만들어도 오류 없이 다시 만들어지는지 확인한다.
7. 준비가 되면 스팀 설정을 추가해 스탠드얼론으로 실행하고 서브시스템 이름이 바뀌는지 확인한다.

같은 레벨에 모이는 것까지가 이 장의 범위입니다. 체력과 발사의 동기화는 Chapter 32부터 다룹니다.

### 연습문제

1. 문제: 세션 코드를 게임모드가 아니라 게임 인스턴스에 두는 이유를 한 문장으로 쓰세요.  
   - 조건: 레벨 이동을 언급할 것
2. 문제: `CreateSession`을 호출한 바로 다음 줄에서 `ServerTravel`을 하지 않고 완료 델리게이트에서 하는 이유를 한 문장으로 쓰세요.
3. 문제: `ServerTravel`과 `ClientTravel`의 차이를 한 줄씩 쓰세요.

### 정답 포인트

- 게임모드는 레벨이 바뀌면 사라지지만 게임 인스턴스는 프로그램이 꺼질 때까지 유지된다. 생성 요청은 바로 끝나지 않으므로 완료 통지를 받은 뒤에 이동해야 한다. `ServerTravel`은 서버가 호출해 모두 함께 이동하고, `ClientTravel`은 클라이언트 혼자 이동한다.

---

[상위 문서로 돌아가기](./README.md)
