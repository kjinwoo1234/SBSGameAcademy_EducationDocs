# Scene 관리와 생명주기

씬(Scene)은 게임 화면 단위입니다.  
스크립트 생명주기 메서드 호출 순서를 이해하면 디버깅이 쉬워집니다.

## 학습 목표
- `Awake`, `Start`, `Update`, `FixedUpdate` 호출 시점을 설명할 수 있다.
- 씬 전환 코드와 Build Settings 연동을 수행할 수 있다.
- 생명주기 관련 버그를 기본 점검할 수 있다.

## 자주 쓰는 메서드
- `Awake()`: 오브젝트 생성 직후
- `Start()`: 첫 프레임 직전 1회
- `Update()`: 매 프레임
- `FixedUpdate()`: 물리 프레임

## 씬 전환
`SceneManager.LoadScene("SceneName")` 사용 (네임스페이스: `UnityEngine.SceneManagement`)

## 실수 포인트
- Build Settings에 씬을 추가하지 않으면 씬 전환 실패
- 초기화 코드를 `Update`에 넣어 성능 저하 발생
- 물리 이동 코드를 `Update`에서 처리해 떨림 발생

## 단계별 실습
1. `Main`과 `Game` 씬을 각각 생성합니다.
2. 두 씬을 Build Settings에 추가합니다.
3. `SceneLoader` 스크립트에서 키 입력 시 `Game` 씬을 로드합니다.
4. `Awake/Start/Update`에 로그를 넣고 호출 순서를 확인합니다.
5. 물리 이동 로직을 `Update`와 `FixedUpdate`에 각각 넣어 차이를 비교합니다.

## 이해 점검 체크리스트
- `Awake`와 `Start`를 구분해 사용할 수 있는가?
- 씬 전환 실패 시 Build Settings를 먼저 점검하는가?
- 물리 관련 코드를 `FixedUpdate`에서 처리했는가?

## 다음 학습 추천
- [입력 처리와 캐릭터 이동](./05-input-movement.md)

## 셀프 퀴즈
1. 물리 프레임 단위로 호출되는 메서드는 무엇인가?
2. 씬 전환 시 가장 먼저 점검해야 할 설정은 무엇인가?

## 정답
1. `FixedUpdate()`
2. Build Settings의 씬 등록 상태

---

[상위 문서로 돌아가기](./README.md)
