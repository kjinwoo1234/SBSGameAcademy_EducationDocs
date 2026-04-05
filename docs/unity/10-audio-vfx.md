# Chapter 10 오디오와 VFX

## 학습 목표
- 오디오 소스 재생과 기본 효과를 적용할 수 있다.
- 이벤트 기반으로 시각 효과를 트리거할 수 있다.

## 세부 주제
### 10-1 AudioSource
- 효과음/배경음
### 10-2 VFX 기초
- ParticleSystem
### 10-3 이벤트 연결
- 피격/폭발 연동

## 실습 체크리스트
- 충돌 시 효과음 + 파티클을 동시에 실행한다.

## 본문
### 10-1 AudioSource
- 효과음(SE)과 배경음(BGM)을 분리하면 밸런스 조절이 쉽습니다.
### 10-2 VFX 기초
- 파티클은 피드백 강화를 위한 대표 효과입니다.
### 10-3 이벤트 연결
- 충돌/공격 이벤트 시점에 사운드와 VFX를 함께 실행하면 타격감이 좋아집니다.

약어 설명(VFX):
- **VFX**는 **Visual Effects**의 약자입니다.
- 한국어로는 "시각 효과"이며, 폭발/불꽃/피격 효과처럼 시각적 피드백을 제공합니다.
- 플레이어가 게임 상태를 빠르게 이해하도록 돕는 것이 핵심 목적입니다.

예시 코드:
```csharp
using UnityEngine;

public class HitEffect : MonoBehaviour
{
    [SerializeField] AudioSource hitSound;
    [SerializeField] ParticleSystem hitFx;

    public void PlayHit()
    {
        hitSound.Play();
        hitFx.Play();
    }
}
```

예상 결과:
```text
피격 시 사운드와 파티클 동시 재생
```

코드 해석:
- `PlayHit`에서 오디오와 파티클을 같은 시점에 실행해 피드백을 강화합니다.
- 효과 참조가 비어 있으면 실행되지 않으므로 연결 상태 점검이 필요합니다.

연습문제:
1. 문제: 버튼 클릭 효과음 재생
   - 입력: UI 버튼 클릭
   - 출력: 클릭 사운드
   - 조건: AudioSource 연결
2. 문제: 적 사망 시 폭발 파티클
   - 입력: HP 0
   - 출력: 폭발 효과
   - 힌트: 오브젝트 제거 전에 효과 실행

정답 포인트:
- 오디오/효과 타이밍이 사용자 체감 품질을 좌우
- 참조 누락을 Inspector에서 우선 점검

---

[상위 문서로 돌아가기](./README.md)
