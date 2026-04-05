# Chapter 02 에디터와 씬 기본 사용

## 학습 목표
- Unity 에디터 핵심 창의 역할을 설명할 수 있다.
- 씬 저장과 기본 배치를 수행할 수 있다.

## 세부 주제
### 02-1 핵심 창
- Hierarchy, Scene, Game, Inspector

### 02-2 씬 관리
- 씬 생성/저장

### 02-3 오브젝트 배치
- 이동/회전/크기 조절

## 실습 체크리스트
- 큐브/카메라/라이트를 배치하고 씬을 저장한다.

## 본문
### 02-1 핵심 창
- Hierarchy는 씬 오브젝트 목록, Inspector는 선택 오브젝트 속성 창입니다.
- Scene은 편집 화면, Game은 실행 화면입니다.

### 02-2 씬 관리
- 씬은 게임의 한 화면 단위를 의미합니다.
- 작업 후 `Ctrl+S`로 저장해 변경 유실을 막습니다.

### 02-3 오브젝트 배치
- Translate/Rotate/Scale 툴로 위치/회전/크기를 조절합니다.
- 좌표 축(X,Y,Z) 이해가 3D 배치의 핵심입니다.

용어 설명:
| 용어 | 의미 |
|---|---|
| 씬(scene) | 게임의 한 레벨/화면 단위 |
| Inspector | 선택 오브젝트 속성을 수정하는 창 |

예시 코드:
```csharp
using UnityEngine;

public class SceneNameLog : MonoBehaviour
{
    void Start()
    {
        Debug.Log(UnityEngine.SceneManagement.SceneManager.GetActiveScene().name);
    }
}
```

예상 결과:
```text
현재 활성 씬 이름이 Console에 출력
```

코드 해석:
- `GetActiveScene()`으로 현재 씬 정보를 가져옵니다.
- 씬 이름 로그는 씬 전환 디버깅에 유용합니다.

연습문제:
1. 문제 1 - 기본 배치
   - 문제: 큐브 3개를 배치하고 위치를 다르게 설정하세요.
   - 입력: 없음
   - 출력: Scene 뷰 배치 결과
   - 조건: 각 큐브 이름 다르게 지정
2. 문제 2 - 씬 저장
   - 문제: `MainScene` 이름으로 씬을 저장하세요.
   - 입력: 없음
   - 출력: 저장된 씬 파일
   - 힌트: Assets/Scenes 폴더 사용

정답 포인트:
- 에디터 창 역할을 정확히 알면 작업 속도 상승
- 씬 저장 습관이 데이터 유실을 막음

---

[상위 문서로 돌아가기](./README.md)
