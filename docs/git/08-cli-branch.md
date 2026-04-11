# Chapter 8 CLI 환경에서 브랜치 생성 및 조작하기

## 학습 목표
- CLI로 브랜치를 생성·삭제·전환한다.
- 3-way merge 상황을 설명한다.
- 리베이스와 병합의 트레이드오프를 말로 정리한다.

## 세부 주제
- 브랜치 생성(`switch -c` / `branch`)
- 전환·추적 브랜치
- merge와 공통 조상
- rebase 기본

## 실습 체크리스트
- 기능 브랜치에서 커밋 2개 → `main`에 merge
- 동일 파일 충돌 1회 해결 후 `merge --continue` 또는 완료 확인

## 본문

<a id="ch8-1"></a>
### 8-1 브랜치 생성하기

예시 명령:
```bash
git switch -c feature/cli-demo
# 또는
git branch feature/cli-demo
git switch feature/cli-demo
```

---

<a id="ch8-2"></a>
### 8-2 브랜치 기본 조작하기

- `git branch` 목록, `git branch -d feature/foo` 삭제(머지된 브랜치).
- 원격 추적: `git switch -c feature/foo origin/feature/foo`

---

<a id="ch8-3"></a>
### 8-3 3-way 병합하기

- **3-way merge**는 **양쪽 브랜치와 공통 조상(common ancestor)** 세 커밋을 기준으로 자동 병합합니다.
- 충돌 시 Git이 세 버전 정보를 바탕으로 마커를 남깁니다.

용어 설명:
| 용어 | 의미 |
|------|------|
| 공통 조상 | 두 브랜치가 갈라지기 직전의 같은 커밋 |

예시 명령:
```bash
git checkout main
git merge feature/cli-demo
```

---

<a id="ch8-4"></a>
### 8-4 리베이스하기

- `git rebase main`은 현재 브랜치 커밋들을 **main 최신 위**로 다시 쌓습니다.
- 충돌 시 `git rebase --continue` / 중단은 `git rebase --abort`.

연습문제:
1. 문제: fast-forward merge가 가능한 조건을 한 문장으로 쓰세요.
2. 문제: 팀 규칙이 "main에는 merge commit만"일 때 rebase를 어디(어느 브랜치)에서 주로 쓰게 될지 추론하세요.

정답 포인트:
- 브랜치 = 포인터; merge = 3-way + 충돌 가능; rebase = 선형 이력, 공유 브랜치 주의.

---

[상위 문서로 돌아가기](./README.md)
