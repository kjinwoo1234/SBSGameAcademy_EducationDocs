# Chapter 4 둘 이상의 원격 저장소로 협업하기

## 학습 목표
- 포크(fork) 저장소와 upstream 원격을 구성한다.
- 오픈소스 기여 흐름(PR)을 말로 설명한다.
- 리베이스(rebase)의 목적과 위험을 구분한다.

## 세부 주제
- 포크와 원격 두 개(`origin`, `upstream`)
- upstream으로 PR 보내기
- 리베이스로 커밋 선 정리

## 실습 체크리스트
- 포크한 저장소를 클론하고 `upstream` 추가
- `main`을 upstream과 동기화한 뒤 작업 브랜치에서 PR 준비

## 본문

<a id="ch4-1"></a>
### 4-1 포크: 원격 저장소를 복사해서 새로운 원격 저장소 만들기

- **포크**는 GitHub에서 타인 저장소를 **내 계정 아래 복제**하는 기능입니다. 원본과는 별도의 원격입니다.
- 로컬에서는 `origin`(내 포크)과 `upstream`(원본) 두 URL을 둘 수 있습니다.

예시 명령:
```bash
git remote add upstream https://github.com/ORIGINAL/REPO.git
git remote -v
```

---

<a id="ch4-2"></a>
### 4-2 원본 저장소에 풀 리퀘스트 보내고 병합하기

- 내 포크의 브랜치에서 **원본 저장소로 PR**을 엽니다(base가 원본 `main`).
- 원본 메인테이너가 리뷰 후 **Merge**하면 원본에 반영됩니다.
- 포크의 `main`은 자동으로 갱신되지 않으므로, 주기적으로 upstream을 merge/pull 합니다.

예시 명령(동기화 예):
```bash
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

<a id="ch4-3"></a>
### 4-3 리베이스: 묵은 커밋을 새 커밋으로 이력 조작하기

- **rebase**는 현재 브랜치의 커밋들을 **다른 베이스 위에 다시 쌓는** 동작입니다. 그래프가 일직선에 가깝게 정리됩니다.
- **이미 푸시한 공유 브랜치**에서 리베이스 후 강제 푸시는 팀 규칙과 충돌할 수 있어 주의합니다.

| merge | rebase |
|-------|--------|
| 이력에 병합 커밋이 남을 수 있음 | 선형 이력에 유리 |
| 공유 브랜치에 비교적 안전 | 공유 브랜치에 force push 이슈 |

예시 명령(로컬 정리용):
```bash
git fetch upstream
git rebase upstream/main
```

연습문제:
1. 문제: `origin`과 `upstream`의 차이를 한 문장씩 쓰세요.
2. 문제: 이미 팀원이 같은 브랜치를 체크아웃해 쓰는데 `rebase` 후 `push --force`가 왜 위험한지 설명하세요.

정답 포인트:
- 포크 = 내 원본; PR = 원본에 합치기 요청; rebase = **이력 재배치**, 공유 브랜치에서는 신중히.

---

[상위 문서로 돌아가기](./README.md)
