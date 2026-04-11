# Chapter 05 UI 만들기

## 학습 목표
- 위젯 블루프린트로 HUD·메뉴 레이아웃을 구성할 수 있다.
- 앵커·피벗으로 해상도 대응 레이아웃을 설계하고, 바인딩·버튼 이벤트로 데이터를 연결한다.

## 세부 주제
- 위젯 블루프린트, 패널, 앵커·피벗, 체력바 바인딩, 버튼·커서

## 실습 체크리스트
- **Widget Blueprint**를 만들고 **Canvas Panel** 또는 **Overlay**에 Progress Bar를 배치한다.
- **Bind**로 캐릭터 HP를 표시하고, 버튼 **OnClicked**에서 메뉴를 연다.
- **Set Show Mouse Cursor**로 UI 모드에서 마우스를 보이게 한다.

## 본문

### 5-1 위젯 블루프린트

- **UMG**(Unreal Motion Graphics) 위젯은 **디자이너 탭**에서 배치, **그래프 탭**에서 로직을 연결합니다.
- **Create Widget → Add to Viewport**로 화면에 올립니다.

약어 설명(UMG):
- **UMG**는 **Unreal Motion Graphics**의 약자입니다.
- 한국어로는 보통 "UMG" 또는 "언리얼 UMG UI"로 부르며, 위젯 블루프린트 기반의 **2D UI 시스템**입니다.
- HUD·메뉴·인게임 패널을 **디자이너 친화적으로** 배치하고 이벤트로 연결하기 위해 씁니다.

---

### 5-2 패널이란

- **Canvas Panel**은 자유 배치, **Vertical/Horizontal Box**는 자동 정렬, **Overlay**는 겹침에 적합합니다.
- 목적에 맞는 패널을 고르면 해상도 대응이 쉬워집니다.

---

### 5-3 앵커와 피벗

- **Anchor**는 부모 크기가 변할 때 **어느 기준점에 붙일지**를 정합니다.
- **Pivot**은 위젯 자체의 기준점으로 회전·스케일 중심이 됩니다.

---

### 5-4 체력바 추가하기 — 바인딩

- Progress Bar의 **Percent**를 **Bind**하여 플레이어의 **Health / MaxHealth**를 반환합니다.
- **Property**가 바뀔 때 UI를 갱신하려면 **Event Dispatcher** 또는 **Interface**로 연결할 수 있습니다.

---

### 5-5 버튼 추가하기 — 이벤트·커서

- **OnClicked**에서 레벨 로드, 설정 창 열기 등을 처리합니다.
- UI 전용 **Input Mode UI Only**로 전환하면 게임 조작과 충돌하지 않습니다.
- **Show Mouse Cursor**를 켜고, 닫을 때 **Game Only**로 되돌립니다.

예시: 메뉴 위젯 띄우기(블루프린트 의사 단계)
1. **Widget Blueprint**를 만들고 루트에 **Canvas Panel**을 둔다.
2. 레벨 블루프린트 또는 플레이어 캐릭터에서 **Create Widget**(Class = 위젯) → **Add to Viewport**를 호출한다.
3. 메뉴를 닫을 때 **Remove from Parent**로 위젯을 제거한다.
4. 메뉴가 떠 있는 동안 **Set Input Mode UI Only**와 **Show Mouse Cursor**를 켠다.

절차 해석:
- **Viewport**에 올린 위젯은 **Z-order**와 **입력 모드**에 따라 클릭이 게임 쪽으로 새지 않도록 맞춰야 합니다.

연습문제:
1. 문제: Anchor를 화면 우하단에 고정하려면 어떤 프리셋을 쓰는 것이 자연스러운지 설명하세요.
   - 입력: 해당 없음
   - 출력: 앵커 프리셋 이름 또는 위치 설명
   - 조건: 해상도가 바뀌어도 모서리에 붙는 이유 한 줄
2. 문제: Tick에서 매 프레임 UI를 갱신하는 방식의 단점과 대안을 한 가지씩 쓰세요.
   - 입력: 해당 없음
   - 출력: 단점 1 + 대안 1
   - 조건: 대안으로 이벤트·바인딩·디스패처 중 하나 명시
3. 문제: Progress Bar **Percent**를 **Bind**할 때 반환 타입과 0~1 범위를 어떻게 맞출지 의사코드 한 블록을 써 보세요.
   - 입력: `CurrentHP`, `MaxHP` 변수
   - 출력: 나눗셈·0 나누기 방지를 포함한 식
   - 조건: `MaxHP <= 0`일 때 처리

정답 포인트:
- UMG = 디자이너+그래프; 패널 = 레이아웃 전략; 앵커 = 반응형; 바인딩 = 데이터 연동.

---

[상위 문서로 돌아가기](./README.md)
