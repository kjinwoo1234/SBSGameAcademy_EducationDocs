# Chapter 11 데이터 저장/불러오기

## 학습 목표
- 게임 데이터를 로컬에 저장/복원하는 기본 흐름을 이해한다.
- JSON 기반 직렬화 기초를 익힌다.

## 세부 주제
### 11-1 저장 대상 설계
- 점수/레벨/설정
### 11-2 JSON 저장
- `JsonUtility`
### 11-3 로드와 예외 처리
- 파일 존재 여부 확인

## 실습 체크리스트
- 점수 데이터를 JSON 파일로 저장하고 다시 불러온다.

## 본문
### 11-1 저장 대상 설계
- 저장할 데이터 구조를 먼저 고정하면 유지보수가 쉬워집니다.
### 11-2 JSON 저장
- Unity에서는 `JsonUtility`로 객체를 문자열로 변환할 수 있습니다.
### 11-3 로드와 예외 처리
- 파일이 없거나 형식이 깨진 경우를 대비한 예외 처리가 필요합니다.

예시 코드:
```csharp
using UnityEngine;
using System.IO;

[System.Serializable]
public class SaveData { public int score; }

public class SaveExample : MonoBehaviour
{
    public void Save(int score)
    {
        SaveData data = new SaveData { score = score };
        string json = JsonUtility.ToJson(data);
        File.WriteAllText(Application.persistentDataPath + "/save.json", json);
    }
}
```

예상 결과:
```text
persistentDataPath 경로에 save.json 생성
```

코드 해석:
- `SaveData` 객체를 JSON 문자열로 직렬화해 파일에 기록합니다.
- 저장 경로는 `Application.persistentDataPath`를 사용해 플랫폼별 안전 경로를 따릅니다.

연습문제:
1. 문제: 최고 점수 저장/복원
   - 입력: 점수 값
   - 출력: 저장/불러오기 결과
   - 조건: 파일 미존재 시 기본값 처리
2. 문제: 옵션 데이터 추가
   - 입력: 볼륨/해상도
   - 출력: JSON 확장 저장
   - 힌트: SaveData 필드 추가

정답 포인트:
- 저장 구조를 먼저 설계하고 구현
- 예외 상황(파일 없음/파싱 실패) 처리 필수

---

[상위 문서로 돌아가기](./README.md)
