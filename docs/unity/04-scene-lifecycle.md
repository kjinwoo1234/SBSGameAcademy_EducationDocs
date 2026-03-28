# Scene 관리와 생명주기

씬(Scene)은 게임 화면 단위입니다.  
스크립트 생명주기 메서드 호출 순서를 이해하면 디버깅이 쉬워집니다.

## 자주 쓰는 메서드
- `Awake()`: 오브젝트 생성 직후
- `Start()`: 첫 프레임 직전 1회
- `Update()`: 매 프레임
- `FixedUpdate()`: 물리 프레임

## 씬 전환
`SceneManager.LoadScene("SceneName")` 사용 (네임스페이스: `UnityEngine.SceneManagement`)

## 실수 포인트
- Build Settings에 씬을 추가하지 않으면 씬 전환 실패

---

[상위 문서로 돌아가기](./README.md)
