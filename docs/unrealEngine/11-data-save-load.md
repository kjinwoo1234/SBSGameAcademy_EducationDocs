# Chapter 11 데이터 저장/불러오기

## 학습 목표
- 게임 상태를 저장/복원하는 기본 구조를 이해한다.
- SaveGame 자산을 활용할 수 있다.

## 세부 주제
### 11-1 저장 대상 설계
- 점수/레벨/인벤토리
### 11-2 SaveGame 사용
- 생성/저장/로드
### 11-3 예외 처리
- 파일 없음/버전 변경 대응

## 실습 체크리스트
- 플레이어 점수와 위치를 저장하고 불러온다.

## 본문
### 11-1 저장 대상 설계
- 저장 필드를 먼저 고정하면 데이터 호환성 관리가 쉬워집니다.
### 11-2 SaveGame 사용
- Unreal의 SaveGame 시스템으로 로컬 저장 로직을 구성합니다.
### 11-3 예외 처리
- 저장 파일이 없거나 데이터 버전이 다를 때 기본값 처리 전략이 필요합니다.

예시 코드(SaveGame 생성):
```cpp
UMySaveGame* SaveObj = Cast<UMySaveGame>(
    UGameplayStatics::CreateSaveGameObject(UMySaveGame::StaticClass()));
SaveObj->Score = CurrentScore;
UGameplayStatics::SaveGameToSlot(SaveObj, TEXT("SlotA"), 0);
```

예상 결과:
```text
재시작 후에도 저장된 데이터 복원
```

코드 해석:
- SaveGame 객체에 데이터 필드를 채운 뒤 슬롯 이름으로 저장합니다.
- 슬롯 규칙을 통일하면 저장 파일 관리가 쉬워집니다.

연습문제:
1. 문제: 최고 점수 저장
   - 입력: 현재 점수
   - 출력: 저장 성공/복원 값
   - 조건: 새 기록일 때만 갱신
2. 문제: 세이브 슬롯 분리
   - 입력: 슬롯 ID
   - 출력: 슬롯별 저장/로드
   - 힌트: 슬롯 이름 규칙화

정답 포인트:
- 저장 포맷을 초기에 명확히 설계
- 예외 상황 처리로 데이터 손상 리스크 축소

---

[상위 문서로 돌아가기](./README.md)
