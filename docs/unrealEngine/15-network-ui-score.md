# Chapter 15 네트워크 UI·점수

## 학습 목표

- 로비·접속 UI가 세션 흐름과 어떻게 연결되는지 설명할 수 있다.
- 점수·상태 UI를 복제된 데이터에 묶을 수 있다.
- 로컬 HUD와 전원 공유 UI를 구분해 설계할 수 있다.

## 본문

### 15-1 로비·접속 UI

로비 위젯은 대략 다음 버튼을 가집니다.

- 방 만들기 → Create Session
- 새로고침 → Find Sessions
- 참가 → Join Session
- 게임 시작 → 호스트만 ServerTravel

![로비 접속 UI 플레이 화면: 세션 목록과 참가 버튼이 보이는 예시](./img/15-lobby-play-screen.png)

UI는 **요청만** 보내고, 성공·실패는 콜백에서 텍스트로 표시합니다. 네트워크 로직을 위젯 Tick에 넣지 않습니다.

### 15-2 점수·상태 동기화

점수는 PlayerState에 두고 복제합니다. HUD는 로컬 PlayerState를 읽어 표시합니다. 스코어보드처럼 **모든 플레이어 점수**가 필요하면 GameState가 PlayerArray를 순회해 읽거나, 위젯이 복제된 PlayerState 목록을 표시합니다.

```cpp
void UHudWidget::NativeTick(const FGeometry& Geo, float Delta)
{
	Super::NativeTick(Geo, Delta);
	if (APlayerController* PC = GetOwningPlayer())
	{
		if (AMyPlayerState* PS = PC->GetPlayerState<AMyPlayerState>())
		{
			SetScoreText(PS->GetScore());
		}
	}
}
```

Tick 대신 RepNotify·델리게이트로 갱신하면 더 깔끔합니다. 체력과 같은 패턴입니다.

### 15-3 로컬 UI vs 공유 UI

| UI | 데이터 소스 | 예 |
|---|---|---|
| 로컬 | 내 폰·내 PlayerState | 내 체력바 |
| 공유 | GameState·모든 PlayerState | 스코어보드, 남은 시간 |

![게임 HUD 체력바·점수와 별도 스코어보드 위젯이 함께 보이는 플레이 화면](./img/15-hud-scoreboard.png)

원격 플레이어 화면에 내 HUD를 또 그리지 않도록 `IsLocalController` / `IsLocallyControlled`를 유지합니다.

서버 모델과 접속부터 플레이까지 전체 흐름은 Chapter 16에서 정리합니다.

### 15-4 따라하기 — 로비·점수 UI 연동

1. 로비 위젯에 방 만들기·새로고침·참가·게임 시작을 Chapter 10 세션 호출에 연결한다.
2. 성공·실패 문구를 콜백에서만 갱신한다.
3. 게임 HUD에 내 점수를 PlayerState에서 읽어 표시한다.
4. 스코어보드용 위젯에서 GameState PlayerArray 또는 복제 PlayerState 목록을 순회한다.
5. PIE 2인에서 점수 변화가 양쪽 스코어보드에 보이는지 확인한다.
6. 내 체력바는 로컬만, 스코어보드는 공유인지를 구분해 배치한다.

### 연습문제

1. 문제: 방 만들기 버튼이 호출해야 할 세션 단계를 쓰세요.  
2. 문제: 스코어보드 데이터를 PlayerState가 아니라 GameMode에만 두면 생기는 문제를 한 문장으로 쓰세요.  
3. 문제: 내 체력바와 스코어보드의 데이터 소스 차이를 한 문장으로 쓰세요.

### 정답 포인트

- Create Session. GameMode는 클라에 없음. 체력=로컬 상태, 스코어보드=공유 복제 상태.

---

[상위 문서로 돌아가기](./README.md)
