# Chapter 5 실무 사례와 함께 Git 다루기

## 학습 목표
- amend, cherry-pick, reset, revert, stash의 **적용 시점**을 구분한다.
- 위험한 명령(`reset --hard`, force push)의 영향 범위를 설명한다.

## 세부 주제
- 실습용 브랜치·백업 습관
- 커밋 수정(amend)
- 체리 픽(cherry-pick)
- 리셋(reset)
- 리버트(revert)
- 스태시(stash)

## 실습 체크리스트
- 별도 연습 저장소에서 위 명령을 각각 1회 이상 실행
- `reset --hard` 전에 브랜치 백업(`branch backup/foo`) 습관 적용

## 본문

<a id="ch5-1"></a>
### 5-1 실습을 위한 사전 준비

- **연습 전용 저장소**에서 시도합니다. 실무 저장소에서 `--hard`를 바로 쓰지 않습니다.
- 중요한 실험 전 `git branch backup/before-exp`로 **포인터 백업**을 남깁니다.

---

<a id="ch5-2"></a>
### 5-2 어멘드: 방금 만든 커밋에 추가하고 싶어요

- `git commit --amend`는 **마지막 커밋**을 고칩니다(메시지 수정 또는 빠진 파일 추가).
- **이미 푸시한 커밋**을 amend하면 히스토리가 바뀌므로 이후 `push`에 `--force-with-lease`가 필요할 수 있습니다. 공유 브랜치에서는 비권장입니다.

예시 명령:
```bash
git add forgotten.txt
git commit --amend --no-edit
```

---

<a id="ch5-3"></a>
### 5-3 체리 픽: 저 커밋 하나만 떼서 붙이고 싶어요

- `git cherry-pick <commit>`은 **특정 커밋의 패치만** 현재 브랜치에 적용합니다.
- 핫픽스를 다른 릴리즈 브랜치에 옮길 때 유용합니다.

예시 명령:
```bash
git cherry-pick abc1234
```

---

<a id="ch5-4"></a>
### 5-4 리셋: 옛날 커밋으로 브랜치를 되돌리고 싶어요

- `git reset --soft <commit>`: 커밋만 되돌리고 변경은 스테이징에 남음.
- `git reset --mixed`(기본): 커밋·스테이징 되돌림, 작업 트리 변경은 유지.
- `git reset --hard <commit>`: **작업 트리까지** 해당 커밋 상태로 맞춤. **복구 어려울 수 있음**.

| 모드 | 작업 트리 | 스테이징 |
|------|-----------|----------|
| soft | 유지 | 유지(스테이징됨) |
| mixed | 유지 | 풀림 |
| hard | 커밋에 맞춤 덮어씀 | 커밋에 맞춤 |

---

<a id="ch5-5"></a>
### 5-5 리버트: 이 커밋의 변경 사항을 되돌리고 싶어요

- `git revert <commit>`은 **되돌리는 새 커밋**을 만듭니다. 공유 브랜치에 안전한 편입니다.
- `reset`은 브랜치 포인터를 과거로 옮기고, `revert`는 **이력을 보존**합니다.

예시 명령:
```bash
git revert abc1234
```

---

<a id="ch5-6"></a>
### 5-6 스태시: 커밋은 안 만들고 잠시 치워두기

- `git stash push -m "wip"`는 작업 중인 변경을 **임시 스택**에 넣습니다.
- 브랜치 전환이 급할 때 커밋을 억지로 만들지 않아도 됩니다.
- `git stash pop`으로 다시 적용(충돌 가능); `git stash list`로 목록 확인.

예시 명령:
```bash
git stash push -m "WIP feature"
git checkout main
git pull
git checkout -
git stash pop
```

연습문제:
1. 문제: 공유 `main`에서 잘못된 커밋을 없애야 한다면 `reset --hard`와 `revert` 중 어떤 쪽을 먼저 고려할지 이유와 함께 쓰세요.
2. 문제: stash에 넣은 변경이 pop 시 충돌하면 어떤 상태인지(스테이징/작업 트리) 설명하세요.

정답 포인트:
- 공유 이력 = **revert 우선**; amend/reset/rebase는 **로컬·개인 브랜치**에서 연습.

---

[상위 문서로 돌아가기](./README.md)
