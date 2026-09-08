# Chapter 4 둘 이상의 원격 저장소로 협업하기

## 학습 목표

- 포크(fork) 저장소와 upstream 원격을 구성한다.
- 오픈소스 기여 흐름(PR)을 말로 설명한다.
- 리베이스(rebase)의 목적과 위험을 구분한다.

## 본문

<a id="ch4-1"></a>

### 4-1 포크: 원격 저장소를 복사해서 새로운 원격 저장소 만들기

**포크**는 GitHub에서 타인 저장소를 **내 계정 아래 복제**하는 기능입니다. 원본과는 별도의 원격입니다. 로컬에서는 `origin`을 내 포크, `upstream`을 원본으로 두는 구성이 흔합니다.

GitHub에서 연습용으로 포크한 뒤, 로컬 클론에서 아래 명령을 실행해 보세요. `ORIGINAL`과 `REPO`는 원본 계정·저장소 이름으로 바꿉니다.

```bash
git remote add upstream https://github.com/ORIGINAL/REPO.git
git remote -v
```

예상 결과:

```text
origin    https://github.com/YOU/REPO.git (fetch)
origin    https://github.com/YOU/REPO.git (push)
upstream  https://github.com/ORIGINAL/REPO.git (fetch)
upstream  https://github.com/ORIGINAL/REPO.git (push)
```

**코드 해석.** `remote add`로 두 번째 원격 별칭을 붙입니다. `remote -v`로 fetch/push URL을 확인합니다. Fork·upstream을 전략과 함께 다시 보는 내용은 [Chapter 14](./14-branch-comparison-fork-team-pr.md)에서 이어집니다.

---

<a id="ch4-2"></a>

### 4-2 원본 저장소에 풀 리퀘스트 보내고 병합하기

내 포크의 브랜치에서 **원본 저장소로 PR**을 엽니다. base는 원본 `main`입니다. 원본 메인테이너가 리뷰 후 **Merge**하면 원본에 반영됩니다. 포크의 `main`은 자동으로 갱신되지 않으므로, 주기적으로 upstream을 가져와 맞춥니다.

아래 순서로 포크의 `main`을 upstream과 맞춰 보세요.

```bash
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

예상 결과:

```text
From https://github.com/ORIGINAL/REPO
 * [new branch]      main -> upstream/main
Updating ...
   main -> main
```

이미 최신이면 `Already up to date`가 나옵니다.

**코드 해석.** `fetch upstream`은 원본 정보만 가져옵니다. `merge upstream/main`으로 로컬 `main`에 합친 뒤, `push origin`으로 **내 포크**에 반영합니다. 원본으로의 기여는 GitHub에서 PR을 열어 요청합니다.

---

<a id="ch4-3"></a>

### 4-3 리베이스: 묵은 커밋을 새 커밋으로 이력 조작하기

**rebase**는 현재 브랜치의 커밋들을 **다른 베이스 위에 다시 쌓는** 동작입니다. 그래프가 일직선에 가깝게 정리됩니다. **이미 푸시한 공유 브랜치**에서 리베이스한 뒤 강제 푸시하면 팀원 이력이 어긋날 수 있어 주의합니다.

| merge | rebase |
|-------|--------|
| 이력에 병합 커밋이 남을 수 있음 | 선형 이력에 유리 |
| 공유 브랜치에 비교적 안전 | 공유 브랜치에서 force push 이슈 |

개인 작업 브랜치에서만, 아래처럼 upstream 최신 위에 다시 쌓아 보세요.

```bash
git fetch upstream
git rebase upstream/main
```

예상 결과:

```text
Successfully rebased and updated refs/heads/feature/...
```

충돌이 나면 파일을 고친 뒤 `git add`하고 `git rebase --continue`로 이어갑니다.

**코드 해석.** rebase는 커밋을 **재배치**합니다. 공유 브랜치에서는 merge가 더 안전한 경우가 많고, CLI에서 브랜치와 함께 다루는 법은 [Chapter 8](./08-cli-branch.md)에서도 이어집니다.

### 연습문제

1. 문제: `origin`과 `upstream`의 차이를 한 문장씩 쓰세요.
   - 입력: 없음
   - 출력: origin 한 문장, upstream 한 문장
   - 조건: 포크 기준 역할로 구분할 것

2. 문제: 이미 팀원이 같은 브랜치를 체크아웃해 쓰는데 `rebase` 후 `push --force`가 왜 위험한지 설명하세요.
   - 입력: 없음
   - 출력: 이력 재작성으로 팀원 로컬과 원격을 어긋나게 한다는 요지의 문장
   - 조건: 공유 브랜치 상황을 전제로 쓸 것

3. 문제: `git remote -v`로 origin·upstream이 보이는 상태를 만들고, upstream `main`을 fetch한 결과를 확인하세요.
   - 입력: 포크 클론 + upstream 추가
   - 출력: `remote -v`에 두 원격, `fetch` 후 `upstream/main` 참조 확인
   - 조건: origin을 원본 URL로 바꾸지 말 것

### 정답 포인트

1. origin은 내 포크, upstream은 원본 원격이다.
2. rebase 후 force push는 이미 공유된 커밋 해시를 바꿔 팀원 브랜치와 충돌을 키운다.
3. 포크 기여는 내 origin에 푸시하고, 원본에는 PR로 요청한다. rebase는 이력 재배치이므로 공유 브랜치에서는 신중히 한다.

---

[상위 문서로 돌아가기](./README.md)
