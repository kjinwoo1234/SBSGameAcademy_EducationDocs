# Chapter 5 실무 사례와 함께 Git 다루기

## 학습 목표

- amend, cherry-pick, reset, revert, stash의 **적용 시점**을 구분한다.
- 위험한 명령(`reset --hard`, force push)의 영향 범위를 설명한다.

## 본문

<a id="ch5-1"></a>

### 5-1 실습을 위한 사전 준비

**연습 전용 저장소**에서 시도합니다. 실무 저장소에서 `--hard`를 바로 쓰지 않습니다. 중요한 실험 전에는 백업 브랜치로 **포인터를 남겨** 둡니다.

아래 명령으로 실험 전 백업 포인터를 만들어 보세요.

```bash
git branch backup/before-exp
git branch --list "backup/*"
```

예상 결과:

```text
  backup/before-exp
```

**코드 해석.** 브랜치 이름은 커밋을 가리키는 포인터입니다. 실험이 망가져도 `git switch backup/before-exp`로 그 시점으로 돌아갈 수 있습니다.

---

<a id="ch5-2"></a>

### 5-2 어멘드: 방금 만든 커밋에 추가하고 싶어요

`git commit --amend`는 **마지막 커밋**을 고칩니다. 메시지 수정이나 빠진 파일 추가에 씁니다. **이미 푸시한 커밋**을 amend하면 히스토리가 바뀌므로 이후 푸시에 `--force-with-lease`가 필요할 수 있습니다. 공유 브랜치에서는 비권장입니다.

방금 만든 로컬 커밋에 파일을 빠뜨렸다면, 아래처럼 고쳐 보세요.

```bash
git add forgotten.txt
git commit --amend --no-edit
```

예상 결과:

```text
[main xxxxxxx] 이전과 같은 메시지
 Date: ...
 2 files changed, ...
```

**코드 해석.** `--no-edit`는 메시지는 유지하고 내용만 고칩니다. 커밋 해시가 바뀌므로, 이미 푸시한 커밋이면 일반 `push`가 거절될 수 있습니다.

---

<a id="ch5-3"></a>

### 5-3 체리 픽: 저 커밋 하나만 떼서 붙이고 싶어요

`git cherry-pick <commit>`은 **특정 커밋의 패치만** 현재 브랜치에 적용합니다. 핫픽스를 다른 릴리즈 브랜치에 옮길 때 유용합니다.

대상 커밋 해시를 확인한 뒤, 현재 브랜치에서 아래를 실행해 보세요. `abc1234`는 실제 해시로 바꿉니다.

```bash
git cherry-pick abc1234
```

예상 결과:

```text
[main yyyyyyy] 원본 커밋 메시지
 Date: ...
 1 file changed, ...
```

**코드 해석.** 새 커밋이 현재 브랜치에 생깁니다. 원본 브랜치의 다른 커밋은 따라오지 않습니다. 충돌이 나면 해결 후 `git cherry-pick --continue`로 이어갑니다.

---

<a id="ch5-4"></a>

### 5-4 리셋: 옛날 커밋으로 브랜치를 되돌리고 싶어요

`git reset`은 브랜치 포인터를 과거 커밋으로 옮깁니다. 모드에 따라 작업 트리·스테이징이 달라집니다.

| 모드 | 작업 트리 | 스테이징 |
|------|-----------|----------|
| soft | 유지 | 유지. 스테이징된 상태 |
| mixed | 유지 | 풀림 |
| hard | 커밋에 맞춤. 덮어씀 | 커밋에 맞춤 |

연습 저장소에서 **soft**로만 먼저 시도해 보세요. `OLD`는 되돌릴 커밋 해시입니다.

```bash
git reset --soft OLD
git status
```

예상 결과:

```text
On branch main
Changes to be committed:
  (수정·추가된 파일이 스테이징에 남아 있음)
```

**코드 해석.** `--soft`는 커밋만 되돌리고 변경은 스테이징에 남깁니다. `--hard`는 작업 트리까지 맞추므로 **복구가 어려울 수 있어** 백업 브랜치 없이 쓰지 않습니다.

---

<a id="ch5-5"></a>

### 5-5 리버트: 이 커밋의 변경 사항을 되돌리고 싶어요

`git revert <commit>`은 **되돌리는 새 커밋**을 만듭니다. 공유 브랜치에 비교적 안전합니다. `reset`은 브랜치 포인터를 과거로 옮기고, `revert`는 **이력을 보존**합니다.

공유해도 되는 연습 브랜치에서 아래를 실행해 보세요. `abc1234`는 되돌릴 커밋 해시입니다.

```bash
git revert abc1234
```

예상 결과:

```text
[main zzzzzzz] Revert "원본 메시지"
 1 file changed, ...
```

**코드 해석.** 과거 커밋을 지우는 것이 아니라, 그 변경을 상쇄하는 **새 커밋**을 추가합니다. 이미 푸시한 `main`에서는 `reset --hard`보다 revert를 먼저 고려합니다.

---

<a id="ch5-6"></a>

### 5-6 스태시: 커밋은 안 만들고 잠시 치워두기

`git stash push -m "wip"`는 작업 중인 변경을 **임시 스택**에 넣습니다. 브랜치 전환이 급할 때 커밋을 억지로 만들지 않아도 됩니다. `git stash pop`으로 다시 적용하며 충돌할 수 있고, `git stash list`로 목록을 확인합니다.

아래 순서로 스태시를 한 바퀴 돌려 보세요.

```bash
git stash push -m "WIP feature"
git checkout main
git pull
git checkout -
git stash pop
```

예상 결과:

```text
Saved working directory and index state On feature/...: WIP feature
...
Dropped refs/stash@{0} ...
```

**코드 해석.** stash는 커밋 없이 변경을 치웁니다. `checkout -`는 직전 브랜치로 돌아갑니다. pop 시 충돌하면 일반 병합 충돌처럼 파일을 고친 뒤 스테이징합니다.

### 연습문제

1. 문제: 공유 `main`에서 잘못된 커밋을 없애야 한다면 `reset --hard`와 `revert` 중 어떤 쪽을 먼저 고려할지 이유와 함께 쓰세요.
   - 입력: 없음
   - 출력: revert를 우선하는 이유 한두 문장
   - 조건: 이미 푸시된 공유 브랜치를 전제로 할 것

2. 문제: stash에 넣은 변경이 pop 시 충돌하면 어떤 상태인지 설명하세요.
   - 입력: 없음
   - 출력: 작업 트리에 충돌 마커가 생기고, 해결 후 add가 필요하다는 요지
   - 조건: 스테이징/작업 트리 중 어디를 손보는지 밝힐 것

3. 문제: 연습 저장소에서 `backup/before-exp`를 만든 뒤, 로컬 전용 커밋 하나를 `--amend`로 메시지 또는 파일만 고쳐 보세요.
   - 입력: 아직 푸시하지 않은 최신 커밋
   - 출력: 해시가 바뀐 커밋 1개. 백업 브랜치는 이전 해시를 가리킴
   - 조건: 공유 `main`에 force push하지 말 것

### 정답 포인트

1. 공유 이력에서는 **revert 우선**이다. amend/reset/rebase는 로컬·개인 브랜치에서 연습한다.
2. stash pop 충돌은 병합 충돌과 같이 작업 트리에 마커가 남는다. 해결 후 스테이징한다.
3. `--hard`와 force push는 영향 범위가 크므로 백업 포인터와 연습 저장소에서만 확인한다.

---

[상위 문서로 돌아가기](./README.md)
