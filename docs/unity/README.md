# Unity 자습 자료

에디터에서 **씬을 구성하고 → C# 스크립트로 상호작용·물리·UI**를 쌓은 뒤, **애니메이션·오디오·빌드**까지 이어 가는 순서를 권장합니다. 개별 챕터 문서가 추가되면 각 항목은 **학습 목표 → 본문 → 따라 할 수 있는 예시 → 연습문제** 형태로 읽도록 맞출 수 있습니다.

## 권장 대상

- C# 기초 문법을 바탕으로 게임 제작을 시작하려는 학습자
- Unity 에디터와 컴포넌트 기반 개발 흐름을 체계적으로 익히려는 학습자

## 선수지식

- C# 변수/조건문/반복문/클래스 기초
- Git 기본 사용(선택)

## 완주 후 역량(목표)

- Unity 에디터에서 씬·프리팹·에셋을 구성하고 재사용할 수 있다.
- `MonoBehaviour` 생명주기와 컴포넌트 조합으로 플레이어 입력·충돌·**UI Toolkit** 기반 UI를 구현할 수 있다.
- 물리·애니메이션·오디오를 게임플레이에 연결하고, 한 플랫폼 기준으로 빌드 흐름을 따라 할 수 있다.

메뉴 이름·패키지 구성은 **Unity 에디터 버전**(LTS 권장)에 따라 다를 수 있습니다. 본문과 다르면 **Unity 매뉴얼(공식 문서)**에서 자신이 쓰는 버전 기준으로 용어·메뉴 경로를 확인하세요. (본 과정 학습 문서에는 외부 사이트 링크를 싣지 않습니다. 강사·저자는 문서를 쓰기 **전에** 공식 문서·출판 서적 등으로 내용을 대조합니다.)

## 목차

챕터별 `.md` 파일이 준비되면 아래 블록에 링크를 붙이고, **파일명·번호·README 목차**를 한꺼번에 맞추면 됩니다.

### Part 01. 에디터·씬 기초

- Unity Hub 설치, 에디터·프로젝트 생성, 씬 저장
- Hierarchy, Scene, Game, Inspector, Project 창 역할
- GameObject, Transform(위치·회전·스케일), 부모-자식 계층
- 카메라·조명 기본 배치와 씬 뷰 조작

### Part 02. C# 스크립팅 입문

- 스크립트 생성·부착, `MonoBehaviour`와 주요 콜백(`Awake`, `Start`, `Update` 등)
- `public` 필드·`[SerializeField]`, 에디터에서 값 노출
- `Debug.Log`로 상태 확인, 컴파일 오류 읽기

### Part 03. 입력·플레이어 이동

- **구형 입력(`Input` 클래스, Input Manager)**과 **새 입력(Input System 패키지)**을 **둘 다** 다룬다. 프로젝트에서 쓰는 쪽은 **Project Settings → Player → Other Settings → Active Input Handling**에서 고른다.
  - **Input Manager (Old)**: 전통적인 `Input.GetAxis` 등 **구형 `Input` API**만 사용할 때
  - **Input System Package (New)**: **Input System**만 사용할 때(패키지 설치·입력 액션 자산 설정 전제)
  - **Both**: 예전 자료·새 코드를 한 프로젝트에서 섞어 쓸 때(입문 단계에서는 혼동을 줄이려면 하나만 쓰는 편이 낫다)
- 키보드·마우스로 이동·회전·점프 등 기본 플레이어 조작을 **구형 API 예시**와 **Input System 예시**로 각각 익힌다.
- `Time.deltaTime`과 프레임 독립 이동

### Part 04. 물리·충돌

- `Rigidbody`, `Collider`, `isKinematic` 개념과 사용 시점
- 충돌(`OnCollision`) vs 트리거(`OnTrigger`) 차이
- 레이캐스트(`Physics.Raycast` 등)로 클릭·조준 감지

### Part 05. 프리팹·레벨 구성

- 프리팹 제작·변경 전파, 씬에 배치
- `Instantiate`로 런타임 생성, 간단한 오브젝트 풀 개념(필요 시)

### Part 06. UI(UI Toolkit)

- **UI Toolkit**(구조·스타일을 코드와 분리하는 Unity UI 방식): **UXML**(화면 구조, 마크업)과 **USS**(스타일시트)로 화면 정의
- 씬의 **`UIDocument`**와 **Panel Settings**로 런타임 패널 연결, **`VisualElement`** 트리(부모-자식 UI 요소 계층) 이해
- 버튼·슬라이더·라벨 등 컨트롤 배치와 **이벤트**(클릭·값 변경) 처리, C#에서 `rootVisualElement`로 요소 찾기·값 갱신
- Flex 레이아웃 등으로 해상도·비율 대응 기초(과정에서 **UGUI(Canvas)**는 다루지 않거나 비교만 짧게)

### Part 07. 애니메이션·오디오

- Animator, Animation Clip, 파라미터로 상태 전환
- `AudioSource`·`AudioClip` 재생, 2D/3D 사운드 기초

### Part 08. 씬·데이터·빌드

- `SceneManager`로 씬 전환, 로딩 흐름
- `PlayerPrefs` 등 간단 저장(범위는 과정에서 조정)
- Build Settings, 타깃 플랫폼, 빌드 산출물 확인

### Part 09. 구조·품질(심화·선택)

- 이벤트·인터페이스로 스크립트 결합도 낮추기
- `ScriptableObject`로 데이터 분리
- 프로파일링·프레임 드롭 원인 찾기 기초

---

[상위 문서로 돌아가기](../../README.md)
