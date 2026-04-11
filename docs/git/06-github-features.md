# Chapter 6 GitHub 100% 활용하기

## 학습 목표
- 프로필·README로 자기 소개 저장소를 꾸민다.
- 리뷰하기 좋은 PR 작성 습관을 적용한다.
- 브랜치 보호 규칙의 목적을 설명한다.

## 세부 주제
- 프로필 README(특수 저장소명)
- PR 품질 체크리스트
- PR 되돌리기(Revert merge)
- 브랜치 보호(branch protection)

## 실습 체크리스트
- `USERNAME/USERNAME` 저장소에 프로필 README 추가
- 테스트 저장소에서 `main` 보호 규칙(직접 푸시 금지 등) 시험 설정

## 본문

<a id="ch6-1"></a>
### 6-1 GitHub 프로필 꾸미기

- **프로필 README**는 GitHub 사용자명과 **동일한 이름의 저장소**(`octocat/octocat`)에 `README.md`를 두면 프로필에 표시됩니다.
- 핀(pin)으로 대표 프로젝트 6개를 고정할 수 있습니다.

---

<a id="ch6-2"></a>
### 6-2 더 좋은 풀 리퀘스트 만들기

- 제목: 변경의 **한 줄 요약**(동사로 시작하면 읽기 쉬움).
- 본문: **배경·스크린샷·재현 절차·리스크·체크리스트**.
- 작은 PR일수록 리뷰 속도와 품질이 좋아집니다.

| 나쁜 예 | 좋은 예 |
|---------|---------|
| "수정" | "Fix null crash when user logs out" |
| 변경 파일만 나열 | 왜 바꿨는지 + 테스트 방법 |

---

<a id="ch6-3"></a>
### 6-3 GitHub에서 풀 리퀘스트 되돌리기

- 머지된 PR은 **Revert** 버튼으로 **되돌리는 새 PR**을 만들 수 있습니다(merge commit 기준).
- 로컬에서도 `git revert -m 1 <merge-commit>` 패턴이 쓰이지만, GitHub UI가 입문에 친숙합니다.

---

<a id="ch6-4"></a>
### 6-4 브랜치 보호하기

- **Branch protection**은 `main` 등 중요 브랜치에 **직접 푸시 금지**, **PR 필수**, **리뷰 승인**, **CI 통과**를 강제합니다.
- 실수로 `main`을 덮어쓰는 사고를 줄이는 데 효과적입니다.

Settings → Branches → Branch protection rules에서 설정합니다.

연습문제:
1. 문제: 브랜치 보호에서 "Require pull request before merging"을 켜면 팀 워크플로가 어떻게 바뀌는지 설명하세요.
2. 문제: 머지된 PR을 웹에서 Revert했을 때 저장소 이력에 남는 것은 무엇인가요?

정답 포인트:
- 프로필 = 특수 저장소 README; PR = 맥락·작게; 보호 규칙 = `main` 사고 방지.

---

[상위 문서로 돌아가기](./README.md)
