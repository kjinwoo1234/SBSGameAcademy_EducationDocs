# Chapter 07 물리와 충돌

## 학습 목표
- 충돌 채널과 물리 시뮬레이션 기본을 이해한다.
- 충돌 이벤트를 처리할 수 있다.

## 세부 주제
### 07-1 충돌 설정
- Collision Preset
### 07-2 물리 시뮬레이션
- Simulate Physics
### 07-3 충돌 이벤트
- Overlap/Hit

## 실습 체크리스트
- 충돌 시 로그 출력과 데미지 처리를 구현한다.

## 본문
### 07-1 충돌 설정
- 충돌 채널 설정으로 어떤 객체와 상호작용할지 결정합니다.
### 07-2 물리 시뮬레이션
- 물리 활성화 시 중력/힘/충돌 응답이 적용됩니다.
### 07-3 충돌 이벤트
- Hit는 물리 충돌, Overlap은 겹침 감지 중심 이벤트입니다.

예시 코드(Overlap 처리):
```cpp
void AItem::NotifyActorBeginOverlap(AActor* OtherActor)
{
    Super::NotifyActorBeginOverlap(OtherActor);
    UE_LOG(LogTemp, Warning, TEXT("Item Overlap"));
}
```

예상 결과:
```text
충돌 시 이벤트 함수 호출 및 로그 출력
```

코드 해석:
- Overlap 이벤트에서 상호작용 시작 지점을 잡아 처리합니다.
- 이벤트 기반 처리로 매 프레임 검사 비용을 줄일 수 있습니다.

연습문제:
1. 문제: 아이템 Overlap 획득 처리
   - 입력: 플레이어 겹침
   - 출력: 아이템 획득
   - 조건: 아이템 비활성화
2. 문제: 벽 Hit 효과 재생
   - 입력: 충돌
   - 출력: 효과음 또는 이펙트
   - 힌트: 이벤트 분기 처리

정답 포인트:
- Overlap/Hit 사용 목적 구분
- 충돌 프리셋 설정이 동작 여부를 좌우

---

[상위 문서로 돌아가기](./README.md)
