# Chapter 08 FX

## 학습 목표
- 나이아가라로 파티클·GPU 이펙트 기본 흐름을 설명할 수 있다.
- 포스트 프로세싱과 3D 사운드의 사용 맥락을 구분한다.

## 세부 주제
- 나이아가라, 포스트 프로세싱, 사운드, 공간음향

## 실습 체크리스트
- **Niagara System**을 만들어 스폰 액터에 붙이거나 **Niagara Component**로 재생한다.
- **Post Process Volume**으로 색보정·블룸을 적용한다.
- **Sound Attenuation**을 켜고 3D 위치에서 거리에 따라 볼륨이 변하는지 확인한다.

## 본문

### 8-1 나이아가라 파티클 시스템

- **Niagara**는 모듈형 파티클·GPU 시뮬레이션에 쓰입니다. **Emitter → System** 계층으로 조합합니다.
- **CPU vs GPU** 파티클은 규모·상호작용 요구에 따라 선택합니다.

---

### 8-2 포스트 프로세싱

- **Post Process Volume**은 **Bloom, Exposure, Color Grading, Vignette** 등 화면 전체 룩을 조정합니다.
- **Infinite Extent**로 전역 적용하거나, 특정 구역만 **Blendable**로 묶을 수 있습니다.

---

### 8-3 사운드 재생

- **Sound Wave / Cue**로 원샷·루프를 재생합니다. **Play Sound at Location**은 월드 3D에 적합합니다.
- **Sound Cue**에서 랜덤 노드·볼륨 노드를 섞으면 발걸음·무기 사운드 변주를 데이터만으로 만들 수 있습니다.

---

### 8-4 3D 공간음향 재생

- **Attenuation**으로 거리 감쇠, **Spatialization**으로 방향감을 줍니다.
- **Occlusion**(가림)은 비용이 있으므로 프로젝트 규모에 맞게 켭니다.
- **Attenuation Settings** 자산을 여러 액터가 공유하면 거리 곡선을 한곳에서 통일 관리할 수 있습니다.

예시: Post Process Volume 적용(재현 절차)
1. **Place Actors**에서 **Post Process Volume**을 레벨에 추가한다.
2. **Infinite Extent (Unbound)**를 켜 전역에 적용하거나, 볼륨 크기로 구역을 제한한다.
3. **Bloom**, **Exposure** 등을 조절한 뒤 뷰포트에서 즉시 변화를 본다.
4. 필요 시 **Blendables**에 커스텀 머티리얼을 추가한다.

절차 해석:
- 포스트는 **최종 프레임 버퍼**를 가공하므로, 과한 스택은 **GPU 채우기(fill rate)** 비용을 키울 수 있습니다.

연습문제:
1. 문제: 파티클을 Niagara 대신 Cascade만 쓰는 신규 프로젝트가 줄어드는 이유(유지보수·기능)를 한 줄로 조사해 적으세요.
   - 입력: 공식 문서 또는 릴리즈 노트
   - 출력: 요약 2문장
   - 조건: "Niagara 권장" 또는 "Cascade 레거시" 중 하나 인용
2. 문제: Post Process를 과하게 쓰면 어떤 성능 지표(FPS 외)에 영향이 갈 수 있는지 추론하세요.
   - 입력: 해당 없음
   - 출력: 지표 이름 + 이유 1문장
   - 조건: GPU 메모리 또는 대역폭 관련 용어 1개
3. 문제: **Play Sound at Location**과 **2D UI 버튼 클릭 사운드**에 각각 적합한 재생 노드를 골라 적으세요.
   - 입력: 해당 없음
   - 출력: 월드용 1개 + UI용 1개
   - 조건: UMG 그래프에서 쓰는 노드 이름을 대략적으로라도 쓸 것

정답 포인트:
- Niagara = 모듈형 FX; Post = 전역 룩; 사운드 = Cue + 감쇠/공간화.

---

[상위 문서로 돌아가기](./README.md)
