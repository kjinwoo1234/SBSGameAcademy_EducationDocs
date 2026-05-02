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

### Part 02. C# 스크립팅 입문

### Part 03. 입력·플레이어 이동

### Part 04. 물리·충돌

### Part 05. 프리팹·레벨 구성

### Part 06. UI(UI Toolkit)

### Part 07. 애니메이션·오디오

### Part 08. 씬·데이터·빌드

### Part 09. 구조·품질(심화·선택)

## 작성 준비중인 내용

- Part 01 세부 내용 보강
  - Unity Hub 설치, 에디터·프로젝트 생성, 씬 저장
  - Hierarchy/Scene/Game/Inspector/Project 창 역할
  - GameObject, Transform, 부모-자식 계층
  - 카메라·조명 기본 배치와 씬 뷰 조작
- Part 02 세부 내용 보강
  - 스크립트 생성·부착, `MonoBehaviour` 주요 콜백(`Awake`, `Start`, `Update`)
  - `public` 필드·`[SerializeField]`와 에디터 값 노출
  - `Debug.Log` 기반 상태 확인과 컴파일 오류 읽기
- Part 03 세부 내용 보강
  - 구형 `Input` API와 Input System 비교 및 프로젝트 설정(`Active Input Handling`)
  - 키보드·마우스 이동/회전/점프 예시
  - `Time.deltaTime` 기반 프레임 독립 이동
- Part 04 세부 내용 보강
  - `Rigidbody`, `Collider`, `isKinematic` 사용 시점
  - 충돌(`OnCollision`) vs 트리거(`OnTrigger`) 차이
  - `Physics.Raycast` 기반 감지
- Part 05 세부 내용 보강
  - 프리팹 제작·변경 전파
  - `Instantiate` 생성과 간단 오브젝트 풀
- Part 06 세부 내용 보강
  - UI Toolkit: UXML/USS, `UIDocument`, Panel Settings
  - `VisualElement` 트리와 이벤트 처리
  - 해상도 대응(Flex 레이아웃)
- Part 07 세부 내용 보강
  - Animator/Animation Clip/파라미터 전환
  - `AudioSource`/`AudioClip`, 2D/3D 오디오
- Part 08 세부 내용 보강
  - `SceneManager` 씬 전환
  - `PlayerPrefs` 기초 저장
  - Build Settings와 타깃 플랫폼 빌드
- Part 09 세부 내용 보강
  - 이벤트·인터페이스로 결합도 낮추기
  - `ScriptableObject` 데이터 분리
  - 프로파일링·프레임 드롭 원인 찾기

---

[상위 문서로 돌아가기](../../README.md)
