# SVN 미니 프로젝트: 릴리스 운영 시뮬레이션

이 문서는 SVN 협업에서 자주 사용하는 릴리스 운영 흐름을 실습하는 통합 프로젝트입니다.

## 학습 목표
- `trunk/branches/tags` 구조를 실제 운영 시나리오에 적용할 수 있다.
- 업데이트/충돌 해결/커밋 절차를 안정적으로 수행할 수 있다.
- 릴리스 태그 생성과 검증 기록을 남길 수 있다.

## 프로젝트 시나리오
- 팀 구성: 2명(또는 1인 2역)
- 작업 1: 입력 검증 로직 보강
- 작업 2: 에러 메시지 표시 개선
- 목표: 브랜치 작업 후 trunk 병합, `release-1.0.0` 태그 생성

## 단계별 실습
1. 저장소 체크아웃 후 `svn update`로 기준 동기화
2. 기능 브랜치 생성: `branches/feature-input-check`
3. 브랜치에서 수정/커밋 후 trunk로 병합
4. 병합 과정 충돌 발생 시 수동 해결 후 `svn resolve`
5. trunk 기준 테스트 완료 후 `tags/release-1.0.0` 생성
6. 릴리스 노트(변경 목적/검증 결과) 작성

## 필수 명령어 예시
```bash
svn update
svn copy ^/trunk ^/branches/feature-input-check -m "기능 브랜치 생성"
svn merge ^/branches/feature-input-check
svn resolve --accept working src/input_validation.cpp
svn copy ^/trunk ^/tags/release-1.0.0 -m "릴리스 태그 생성"
```

## 검증 기준
- 업데이트 후 작업해 충돌 위험을 줄였는가?
- 충돌 해결 기록과 테스트 결과를 남겼는가?
- 태그 생성 시점이 릴리스 기준과 일치하는가?

## 제출 산출물
1. 브랜치/태그 생성 명령 기록
2. 충돌 해결 내역(있다면) 및 검증 결과
3. 릴리스 노트(변경 요약 + 테스트 항목)

## 셀프 퀴즈
1. `tags`를 릴리스 기준점으로 쓰는 이유는?
2. `svn update`를 생략하면 어떤 위험이 커지는가?

## 정답
1. 배포 기준 리비전을 고정해 재현성과 롤백 가능성을 확보하기 위해서
2. 최신 변경 미반영으로 충돌과 회귀 위험이 증가하기 때문

---

[SVN 자습 자료로 돌아가기](./README.md)
