# 데이터 저장 (PlayerPrefs, JSON)

게임 설정/진행 데이터를 저장하는 기본 방법입니다.

## PlayerPrefs
- 간단한 값 저장에 적합 (볼륨, 최고점수)

```csharp
PlayerPrefs.SetInt("HighScore", 1200);
int score = PlayerPrefs.GetInt("HighScore", 0);
```

## JSON 저장
- 구조화된 데이터 저장에 적합
- `JsonUtility` + 파일 쓰기/읽기 사용

## 실수 포인트
- PlayerPrefs는 보안 저장소가 아님 (치트 가능성)

---

[상위 문서로 돌아가기](./README.md)
