# Chapter 11 체력 리플리케이션

## 학습 목표

- 체력을 서버에서만 변경하는 이유를 설명할 수 있다.
- `Replicated`와 `RepNotify`를 구분해 체력 UI에 적용할 수 있다.
- 단순 상태 동기화 흐름을 코드로 연결할 수 있다.

## 본문

### 11-1 서버가 체력을 소유한다

클라이언트에서 `Health -= Damage`만 하면 그 화면에서만 줄어들고, 다른 사람·서버와 어긋납니다. 또한 치트가 쉬워집니다.

권장 흐름:

1. 피격 판정은 서버에서 확정한다.
2. 서버가 `Health`를 줄인다.
3. 복제로 클라이언트 `Health`가 맞춰진다.
4. UI는 복제된 값을 본다.

### 11-2 RepNotify

`UPROPERTY(ReplicatedUsing = OnRep_Health)`처럼 쓰면 값이 복제될 때 `OnRep_Health`가 호출됩니다. 체력바 갱신·피격 이펙트처럼 **“바뀐 순간”** 반응이 필요할 때 맞습니다.

```cpp
UPROPERTY(ReplicatedUsing = OnRep_Health)
float Health;

void AMyCharacter::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& Out) const
{
	Super::GetLifetimeReplicatedProps(Out);
	DOREPLIFETIME(AMyCharacter, Health);
}

void AMyCharacter::OnRep_Health()
{
	// 로컬 HUD 체력바 갱신
}
```

서버에서 체력을 바꿀 때도 UI를 갱신해야 한다면, 변경 함수 끝에서 서버 전용 갱신을 호출하고, 클라이언트는 `OnRep_Health`에 맡기는 식으로 나눕니다.

### 11-3 단순 상태 예

살아 있음/사망 플래그, 팀 번호처럼 작은 상태도 같은 패턴입니다. 모든 변수를 복제하지 마세요. **다른 클라이언트도 봐야 하는 것**만 고릅니다. 로컬 조준 UI 위치 등은 복제하지 않는 편이 낫습니다.

발사·데미지 순간 판정은 Chapter 12에서 다룹니다. 이 장은 체력 숫자가 창마다 같아지는 데 집중합니다.

### 11-4 따라하기 — RepNotify 체력 동기화

1. 체력에 `ReplicatedUsing = OnRep_Health`와 `DOREPLIFETIME`을 연결한다.
2. 서버만 `Health`를 깎는 함수를 둔다.
3. PIE 플레이어 2, Listen Server로 실행한다.
4. 한쪽에서 피해를 주고, 양쪽 HUD·로그의 체력이 같은지 확인한다.
5. `OnRep_Health`에 로그를 남겨 클라이언트에서 호출되는지 확인한다.

### 연습문제

1. 문제: 체력을 클라이언트에서만 깎을 때 생기는 문제 두 가지를 쓰세요.  
2. 문제: 체력바에 RepNotify가 잘 맞는 이유를 한 문장으로 쓰세요.  
3. 문제: 복제하지 않아도 되는 로컬 전용 데이터 예를 하나 드세요.

### 정답 포인트

- 불일치·치트. 변경 순간 UI 반응. 조준점 위치 등 로컬 UI.

---

[상위 문서로 돌아가기](./README.md)
