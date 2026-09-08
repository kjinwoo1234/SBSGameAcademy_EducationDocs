# Chapter 09 멀티플레이 기초

## 학습 목표

- 클라이언트-서버 모델에서 권한이 어디에 있는지 설명할 수 있다.
- Server / Client / Multicast RPC와 **오너십**의 관계를 구분해 설명할 수 있다.
- Replication이 RPC와 어떻게 다른지 한 줄로 말할 수 있다.
- PIE Listen Server로 RPC·복제 동작을 확인할 수 있다.

## 본문

### 09-1 클라이언트-서버 모델

멀티플레이에서 **서버**는 게임 상태의 권한 있는 기준인 경우가 많습니다. 클라이언트는 입력을 보내고, 서버가 확정한 결과를 받아 화면을 맞춥니다.

치트와 불일치를 줄이려면 데미지·점수처럼 **중요한 판정은 서버**에서 합니다. 클라이언트는 이펙트를 먼저 보여 줄 수는 있어도, 최종 체력은 서버 값을 따릅니다.

### 09-2 RPC

**RPC**는 Remote Procedure Call입니다. 다른 네트워크 역할의 기기에서 함수를 실행하도록 요청합니다.

| 종류 | 누가 실행 | 예시 |
|---|---|---|
| Server RPC | 서버 | 발사 요청, 상호작용 요청 |
| Client RPC | 특정 클라이언트 | 그 플레이어만 보는 UI 알림 |
| Multicast RPC | 관련 클라이언트들 | 폭발 이펙트·사운드 |

```cpp
UFUNCTION(Server, Reliable)
void Server_RequestFire();

UFUNCTION(NetMulticast, Unreliable)
void Multicast_PlayMuzzleFX();
```

**Reliable**은 도달을 더 보장하려 하고, **Unreliable**은 이펙트처럼 가끔 빠져도 되는 것에 씁니다. 남용하면 대역폭과 지연이 커집니다.

호출할 때는 선언한 이름 `Server_RequestFire()`를 씁니다. 서버에서 돌릴 본문은 엔진 규칙으로 `Server_RequestFire_Implementation`에 둡니다. 구현 예는 Chapter 12에서 이어서 봅니다.

### 09-3 RPC와 오너십

**오너십**은 “이 액터·연결을 어느 클라이언트가 소유하는가”입니다. 폰·플레이어 컨트롤러·일부 무기는 특정 연결의 **Owner**에게 묶입니다.

Server RPC를 누가 호출할 수 있는지는 오너십과 맞물립니다.

1. 보통 **자신의 폰·컨트롤러**에서 Server RPC를 보냅니다. 내가 소유한 액터의 함수를 호출하는 그림입니다.
2. 남이 소유한 액터에 아무 Server RPC나 호출하게 두면, 치트·잘못된 판정으로 이어지기 쉽습니다.
3. 서버는 호출자가 그 액터를 소유하는지, 거리·상태가 유효한지 검사하는 습관을 둡니다. 필요하면 `WithValidation`으로 `_Validate` 함수를 함께 둡니다.
4. Owner가 없는 액터, 서버만 다루는 액터는 역할이 다릅니다. Authority·Role 이름은 Chapter 13에서 표로 정리합니다. 이 장에서는 **“내 입력 → 내 소유 액터의 Server RPC → 서버 판정”**만 고정합니다.

오류 예: 클라이언트 A가 클라이언트 B의 캐릭터에서 `Server_RequestFire`를 호출하게 설계함. 소유권이 없는 쪽의 발사가 서버로 가거나, 엔진이 호출을 막아 아무 일도 안 일어납니다.

### 09-4 Replication

**Replication**은 서버가 가진 **프로퍼티 상태**를 클라이언트의 같은 액터에 맞추는 메커니즘입니다. RPC가 “한 번 호출”에 가깝다면, 복제는 “값이 계속 같아야 하는 상태”에 가깝습니다.

액터에서 `bReplicates = true`를 켜고, 변수에 `UPROPERTY(Replicated)`를 둡니다. `GetLifetimeReplicatedProps`에 `DOREPLIFETIME`을 등록하는 패턴이 일반적입니다. 체력 RepNotify 코드는 Chapter 11에서 연결합니다.

### 09-5 따라하기 — RPC·Replication 테스트

테스트는 에디터 **PIE**로 합니다. PIE는 패키지로 빌드하지 않고 에디터 안에서 바로 플레이하는 모드입니다.

1. PIE 설정에서 플레이어 수를 2로 둔다.
2. Net Mode를 **Listen Server**로 고른다. Listen Server는 한 프로세스가 서버 역할과 호스트 플레이어 역할을 같이 하는 방식이다.

![에디터 Play 설정: Number of Players가 2, Net Mode가 Listen Server로 선택된 패널](./img/09-pie-listen-server-settings.png)

![PIE로 창 두 개가 떠서 호스트·클라이언트가 같은 맵을 플레이하는 화면](./img/09-pie-two-windows.png)

3. 창 두 개에서 각각 캐릭터가 보이는지 확인한다.
4. 호스트·클라이언트 각각에서 이동이 보이는지 확인한다.
5. Server RPC로 보내는 로그를 서버 창에서만 확인한다.
6. 복제된 변수를 바꿔 양쪽 값이 같아지는지 확인한다.

전용 서버는 서버만 돌리고 플레이어는 전부 클라이언트로 둡니다. 비교는 Chapter 16에서 이어갑니다. 세션·레벨 이동은 Chapter 10, 체력 RepNotify는 Chapter 11에서 이어갑니다.

### 연습문제

1. 문제: 데미지 확정을 클라이언트에서만 하면 생기는 문제를 한 문장으로 쓰세요.  
2. 문제: Server RPC 호출과 오너십이 어긋나면 생기는 문제를 한 문장으로 쓰세요.  
3. 문제: Replication과 Server RPC의 차이를 두 문장으로 쓰세요.

### 정답 포인트

- 치트·불일치. 남의 액터 발사·호출 실패. 복제=상태 동기화, Server RPC=서버에서 함수 실행 요청.

---

[상위 문서로 돌아가기](./README.md)
