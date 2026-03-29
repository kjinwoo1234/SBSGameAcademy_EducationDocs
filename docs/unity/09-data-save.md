# 데이터 저장 (PlayerPrefs, JSON)

게임 설정/진행 데이터를 저장하는 기본 방법입니다.

## 학습 목표
- PlayerPrefs와 JSON 저장 방식 차이를 설명할 수 있다.
- 옵션값/점수 데이터를 저장하고 로드할 수 있다.
- 저장 실패 시 점검 항목을 적용할 수 있다.

## PlayerPrefs
- 간단한 값 저장에 적합 (볼륨, 최고점수)

```csharp
PlayerPrefs.SetInt("HighScore", 1200);
int score = PlayerPrefs.GetInt("HighScore", 0);
PlayerPrefs.Save();
```

## JSON 저장
- 구조화된 데이터 저장에 적합
- `JsonUtility` + 파일 쓰기/읽기 사용

## 전체 코드 예시 (JSON 저장/로드)
```csharp
using System;
using System.IO;
using UnityEngine;

[Serializable]
public class SaveData
{
    public string playerName;
    public int level;
    public int gold;
}

public class SaveManager : MonoBehaviour
{
    private string SavePath => Path.Combine(Application.persistentDataPath, "save.json");

    public void Save()
    {
        SaveData data = new SaveData
        {
            playerName = "홍길동",
            level = 3,
            gold = 1200
        };

        string json = JsonUtility.ToJson(data, true);
        File.WriteAllText(SavePath, json);
    }

    public SaveData Load()
    {
        if (!File.Exists(SavePath))
            return null;

        string json = File.ReadAllText(SavePath);
        return JsonUtility.FromJson<SaveData>(json);
    }
}
```

## 미니 실습 과제
1. 볼륨 값을 PlayerPrefs로 저장/불러오기 합니다.
2. 플레이어 이름/레벨/골드를 JSON으로 저장합니다.
3. 재실행 후 저장된 데이터를 UI에 출력합니다.

## 실수 포인트
- PlayerPrefs는 보안 저장소가 아님 (치트 가능성)
- 키 이름 오타로 로드 실패하는 실수
- 기본값 처리 누락으로 예외가 발생하는 실수

## 이해 점검 체크리스트
- [ ] PlayerPrefs와 JSON의 용도를 구분할 수 있는가?
- [ ] 저장 실패 시 기본값 처리 로직이 있는가?
- [ ] 저장 키를 상수로 관리하고 있는가?

## 다음 학습 추천
- [빌드와 배포](./10-build-deploy.md)

## 셀프 퀴즈
1. 간단한 옵션값 저장에 적합한 방식은 PlayerPrefs와 JSON 중 무엇인가?
2. JSON 파일 저장 경로로 Unity에서 자주 사용하는 경로는 무엇인가?

## 정답
1. PlayerPrefs
2. `Application.persistentDataPath`

---

[상위 문서로 돌아가기](./README.md)
