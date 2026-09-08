# Chapter 7 CLI 환경에서 Git 명령어 살펴보기

## 학습 목표
- CLI를 쓰는 이유를 실무 관점에서 설명한다.
- Git Bash를 열고 경로 이동·저장소 상태를 확인한다.
- 기본 명령과 원격 관련 명령을 구분해 사용한다.

## 본문

<a id="ch7-1"></a>
### 7-1 왜 CLI를 사용할까?

**CLI**는 **Command Line Interface**의 약자로, 한국어로는 “명령줄 인터페이스”입니다. 마우스로 버튼을 누르는 GUI와 달리, 텍스트 명령으로 Git을 조작합니다. **스크립트 자동화**, **서버 SSH 환경**, **상세 옵션 제어**에는 CLI가 GUI보다 유리한 경우가 많습니다. 앞 장까지는 SourceTree·웹 UI로 감을 익혔고, 이 장부터는 같은 작업을 명령으로 옮깁니다.

아래 표를 읽고 GUI와 CLI의 차이를 한 번씩 말로 정리해 보세요.

| GUI | CLI |
|-----|-----|
| 시각적·입문에 쉬움 | 로그·옵션·자동화에 강함 |
| 화면마다 다름 | 명령어는 도구 간 동일 개념 |

**표 해석**
- GUI는 클릭 위치가 도구마다 달라질 수 있습니다.
- CLI는 `git status`처럼 **같은 이름**으로 상태를 물을 수 있어 문서·자동화에 맞습니다.

---

<a id="ch7-2"></a>
### 7-2 Git Bash 시작하기

Windows에서는 **Git for Windows**에 포함된 **Git Bash**가 흔합니다. Bash는 명령을 입력하는 창입니다. `pwd`는 지금 폴더 경로를 보여 주고, `ls`는 목록을, `cd 폴더`는 그 폴더로 이동합니다. 경로에 공백이 있으면 따옴표로 감쌉니다.

Git Bash를 연 뒤, 아래를 순서대로 입력해 보세요. `my-repo`는 본인 연습 저장소 경로로 바꿉니다.

```bash
cd "/c/work/my-repo"
pwd
```

**예상 출력**

```text
/c/work/my-repo
```

**명령 해석**
- `cd "…"` — 작업할 저장소 폴더로 이동합니다. 따옴표는 공백 있는 경로를 하나의 인자로 묶습니다.
- `pwd` — 이동이 맞았는지 현재 경로를 확인합니다.

저장소 루트에 왔으면 `git status`로 Git이 이 폴더를 저장소로 아는지 먼저 확인합니다. 출력에 `On branch` 같은 줄이 보이면 준비된 상태입니다.

---

<a id="ch7-3"></a>
### 7-3 기본 Git 명령어 살펴보기

로컬에서 자주 쓰는 흐름은 **상태 보기 → 차이 확인 → 스테이징 → 커밋 → 로그 확인**입니다. 먼저 상태와 차이를 보는 명령부터 실행해 보세요.

```bash
git status
git diff
```

**예상 출력**

```text
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   README.md
```

`git diff`는 아직 스테이징하지 않은 줄 단위 차이를 보여 줍니다. 변경이 없으면 출력이 비어 있을 수 있습니다.

**명령 해석**
- `git status` — 현재 브랜치와 수정·스테이징 상태를 요약합니다.
- `git diff` — 작업 트리와 스테이징 영역 사이의 내용 차이를 봅니다.

이어서 스테이징과 커밋입니다. `-m`은 커밋 메시지를 **같은 줄에 문자열로** 붙일 때 씁니다. `add -p`는 파일 전체가 아니라 **변경 덩어리를 골라** 스테이징할 때 씁니다. 처음이면 `git add README.md`처럼 파일 단위로 연습해도 됩니다.

```bash
git add README.md
git commit -m "Update README"
git log --oneline -n 5
```

**예상 출력**

```text
a1b2c3d Update README
f6e5d4c Previous commit
```

**명령 해석**
- `git add README.md` — 해당 파일을 다음 커밋 후보로 올립니다.
- `git commit -m "…"` — `-m` 뒤 따옴표 문자열이 커밋 메시지입니다.
- `git log --oneline -n 5` — 최근 커밋을 한 줄 요약으로 최대 5개 보여 줍니다.

`git add -p`와 `git log --graph --decorate` 같은 옵션은 익숙해진 뒤 같은 명령에 붙여 확장하면 됩니다. 브랜치를 만들고 합치는 명령은 다음 장에서 다룹니다.

---

<a id="ch7-4"></a>
### 7-4 원격 저장소 관련 Git 명령어 살펴보기

원격 작업은 **어디를 원격으로 아는지 확인**한 뒤, **가져오기**와 **보내기**를 구분합니다. 아래를 실행해 등록된 원격 이름을 확인해 보세요.

```bash
git remote -v
```

**예상 출력**

```text
origin  https://github.com/example/my-repo.git (fetch)
origin  https://github.com/example/my-repo.git (push)
```

**명령 해석**
- `git remote -v` — `-v`는 상세 보기입니다. 원격 이름과 URL이 함께 나옵니다. 보통 이름이 `origin`입니다.

가져오기와 보내기는 역할이 다릅니다. 아래 표를 먼저 읽고, 연습 저장소에서 `fetch`와 `status`만 순서대로 실행해 보세요.

| 명령 | 하는 일 |
|------|---------|
| `git fetch origin` | 원격 갱신 정보를 가져옴. 로컬 `main` 커밋은 바로 안 바뀔 수 있음 |
| `git pull origin main` | fetch 후 현재 브랜치에 통합. 설정에 따라 merge 또는 rebase |
| `git push origin main` | 로컬 커밋을 원격 `main`으로 보냄 |

```bash
git fetch origin
git status
```

**명령 해석**
- `fetch`만 하면 원격 추적 정보(`origin/main` 등)가 갱신되고, 내가 체크아웃한 `main` 끝은 그대로일 수 있습니다.
- 그래서 `git log main`과 `git log origin/main`이 달라 보일 수 있습니다.
- `pull`은 가져오면서 현재 브랜치까지 맞추고, `push`는 내 커밋을 원격으로 보냅니다. `push`가 거절되면 원격에 없는 새 커밋이 있는 경우가 많아, 먼저 `fetch`/`pull`로 맞춘 뒤 다시 보냅니다.

이 장 범위는 CLI 이유·Bash 이동·로컬 기본 명령·원격 fetch/pull/push 구분입니다. 브랜치 생성·병합·리베이스·태그는 다음 장에서 이어갑니다.

### 연습문제

**문제 1**
- 문제: `fetch`만 했을 때 `git log origin/main`과 `git log main`이 달라질 수 있는 이유를 두 문장 이내로 설명하세요.
- 입력: 없음. 본문 7-4를 근거로 서술
- 출력: fetch는 원격 추적 정보를 갱신하고 로컬 `main` 끝은 바로 안 바꿀 수 있다는 설명이 포함된 문장
- 조건: “그냥 다르다”만 쓰지 말 것

**문제 2**
- 문제: `git push`가 rejected될 때 가장 먼저 의심할 원인 두 가지를 쓰세요.
- 입력: 없음
- 출력: 원인 두 줄. 예: 원격에 로컬에 없는 커밋이 있음 / 권한·보호 규칙으로 직접 푸시가 막힘
- 조건: 본문 7-4와 앞 장 브랜치 보호 맥락 중 최소 하나를 포함해도 됨

**문제 3**
- 문제: `git commit -m "Fix typo"`에서 `-m`과 따옴표의 역할을 각각 한 줄로 쓰세요.
- 입력: 없음
- 출력: `-m` = 메시지 옵션, 따옴표 = 메시지 문자열 경계(공백 포함)라는 요지
- 조건: 본문 7-3 기준

### 정답 포인트
- 문제 1: `fetch`는 `origin/main` 같은 원격 추적 정보를 갱신하고, 체크아웃 중인 `main` 포인터는 즉시 옮기지 않을 수 있다.
- 문제 2: 원격에 새 커밋이 있어 이력이 갈라짐; 권한 부족 또는 브랜치 보호로 직접 푸시 거부.
- 문제 3: `-m`은 커밋 메시지를 인자로 넘기는 옵션이고, 따옴표는 공백 포함 메시지를 하나의 문자열로 묶는다.

---

[상위 문서로 돌아가기](./README.md)
