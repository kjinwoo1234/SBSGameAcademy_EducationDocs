# Chapter 0 빠른 실습으로 Git, GitHub 감 익히기

## 학습 목표
- Git과 GitHub의 역할 차이를 말로 설명할 수 있다.
- 로컬에서 저장소를 만들고 커밋까지 수행할 수 있다.
- 원격(GitHub)과 연결해 푸시·풀의 흐름을 이해한다.

## 세부 주제
- Git과 GitHub의 관계
- 로컬 저장소 초기화와 첫 커밋
- 원격 저장소 연결과 푸시
- 원격 변경 가져오기(클론/풀)

## 실습 체크리스트
- 새 폴더에서 `git init` 후 커밋 2개 이상 만들기
- GitHub에 빈 저장소를 만들고 `push`까지 성공하기
- `git pull` 또는 `git clone`으로 최신 상태 맞추기

## 본문

<a id="ch0-1"></a>
### 0-1 Git 그리고 GitHub

- **Git**은 컴퓨터 안에서 버전(스냅샷)을 기록하는 **분산 버전 관리 도구**입니다.
- **GitHub**는 Git 저장소를 **인터넷에 올려 두고 협업·백업**할 수 있게 해 주는 **웹 서비스**입니다. (비슷한 서비스로 GitLab, Bitbucket 등이 있습니다.)

약어 설명( DVCS ):
- **DVCS**는 **Distributed Version Control System**의 약자입니다.
- 한국어로는 "분산형 버전 관리 시스템"이며, 여러 개발자가 각자 로컬에 전체 이력 사본을 가질 수 있는 구조를 말합니다.
- 오프라인에서도 커밋을 쌓고, 나중에 원격과 동기화할 수 있어 협업에 널리 쓰입니다.

| 구분 | Git | GitHub |
|------|-----|--------|
| 위치 | 내 PC에 설치되는 프로그램 | 웹사이트(클라우드) |
| 하는 일 | 버전 기록, 브랜치, 병합 | 저장소 호스팅, PR, 이슈 |

예시 명령(참고용, 아래 절에서 순서대로 실행):
```bash
git --version
```

예상 결과:
```text
git version 2.x.x
```

코드 해석:
- Git이 설치되어 있으면 버전 문자열이 출력됩니다. 없다면 공식 사이트에서 설치합니다.

연습문제:
1. 문제: Git과 GitHub 중 "내 노트북에서만 버전을 남기는 데 필수"인 것은 무엇인지 한 문장으로 쓰세요.
2. 문제: 팀원과 같은 코드를 공유하려면 Git만으로 충분한지, 원격 호스팅이 왜 필요한지 설명하세요.

정답 포인트:
- 버전 기록 자체는 Git, **원격 공유·협업 인터페이스**는 GitHub(또는 동급 서비스).

---

<a id="ch0-2"></a>
### 0-2 Git 설치하고 로컬 저장소에서 커밋 관리하기

- 설치 후 터미널(또는 Git Bash)에서 작업 디렉터리로 이동합니다.
- `git init`으로 `.git` 폴더가 생기면 해당 폴더가 **저장소(repository)**가 됩니다.
- `git add`는 다음 커밋에 **포함할 변경**을 고르는 단계, `git commit`은 **스냅샷을 고정**하는 단계입니다.

용어 설명:
| 용어 | 의미 |
|------|------|
| 작업 디렉터리(working tree) | 실제 파일을 편집하는 폴더 |
| 스테이징(staging) | 커밋에 넣을 변경을 임시로 모아 두는 영역 |
| 커밋(commit) | 특정 시점의 저장소 상태를 기록한 단위 |

예시 명령:
```bash
mkdir my-first-repo
cd my-first-repo
git init
echo "# Hello" > README.md
git add README.md
git commit -m "Add README"
```

예상 결과:
```text
Initialized empty Git repository in .../my-first-repo/.git/
[main (root-commit) xxxxxxx] Add README
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

코드 해석:
- `git init`이 저장소를 만듭니다. 기본 브랜치 이름은 환경에 따라 `main` 또는 `master`일 수 있습니다.
- `git add` 후 `git commit`으로 첫 버전이 기록됩니다.

연습문제:
1. 문제: `README.md`를 수정한 뒤 커밋까지 하세요. 커밋 메시지는 변경 내용을 요약하세요.
2. 문제: `git status`를 실행했을 때 "nothing to commit"이 나오기 전에 거쳐야 할 단계 2가지를 순서대로 쓰세요.

정답 포인트:
- 변경 → `add`(스테이징) → `commit`(기록).

---

<a id="ch0-3"></a>
### 0-3 GitHub 원격 저장소에 커밋 올리기

- GitHub에서 **빈 저장소**를 만든 뒤 표시되는 URL(HTTPS 또는 SSH)을 복사합니다.
- `git remote add origin <URL>`로 로컬과 원격을 연결합니다.
- `git push -u origin main`(또는 `master`)으로 첫 푸시를 합니다. `-u`는 이후 `git push`만으로 같은 업스트림을 쓰도록 설정합니다.

용어 설명:
| 용어 | 의미 |
|------|------|
| 원격(remote) | GitHub 등 네트워크 상의 저장소 별칭(보통 `origin`) |
| 푸시(push) | 로컬 커밋을 원격에 올리는 동작 |

예시 명령:
```bash
git remote add origin https://github.com/USER/REPO.git
git branch -M main
git push -u origin main
```

예상 결과:
```text
Enumerating objects: 3, done.
...
To https://github.com/USER/REPO.git
 * [new branch]      main -> main
```

코드 해석:
- `origin`은 원격 주소에 붙이는 관습적인 이름입니다.
- 인증은 HTTPS(토큰) 또는 SSH 키 방식 중 하나를 사용합니다.

연습문제:
1. 문제: 로컬에만 있는 커밋 1개를 GitHub 웹에서 파일이 보이도록 푸시하세요.
2. 문제: `git remote -v` 출력에서 fetch/push URL이 같은지 확인하고, 왜 두 줄이 나오는지 설명하세요.

정답 포인트:
- 원격 등록 → 브랜치 맞춤 → `push`; 인증 실패 시 토큰/SSH 설정을 점검.

---

<a id="ch0-4"></a>
### 0-4 GitHub 원격 저장소의 커밋을 로컬 저장소에 내려받기

- **클론(clone)**: 원격 저장소 전체를 처음 받아 올 때 사용합니다. (`git clone <URL>`)
- **풀(pull)**: 이미 연결된 저장소에서 **원격의 새 커밋을 가져와 현재 브랜치에 병합**합니다. (`git pull`)
- **페치(fetch)**: 원격 정보만 가져오고 작업 디렉터리는 바로 바꾸지 않습니다. 이후 병합/리베이스로 합칩니다.

| 명령 | 언제 쓰나 |
|------|-----------|
| `git clone` | 처음 프로젝트 받을 때 |
| `git pull` | 팀원이 올린 최신 커밋을 내 브랜치에 반영 |
| `git fetch` | 먼저 원격만 확인하고 합치기 전 검토 |

예시 명령:
```bash
git clone https://github.com/USER/REPO.git
cd REPO
git pull
```

예상 결과:
```text
Already up to date.
```
(원격에 새 커밋이 없을 때)

코드 해석:
- 클론은 `.git` 포함 전체 이력을 복사합니다.
- `pull`은 내부적으로 `fetch` + 병합(또는 설정에 따라 리베이스)에 해당할 수 있습니다.

연습문제:
1. 문제: GitHub 웹에서 README를 수정한 뒤, 로컬에서 `pull`로 반영하세요.
2. 문제: `fetch`만 했을 때와 `pull`했을 때 작업 트리가 달라질 수 있는 이유를 한 문장으로 쓰세요.

정답 포인트:
- 최신 맞추기 = 원격 변경 가져오기 + 로컬 브랜치에 통합(`pull`이 한 번에 처리하는 경우가 많음).

---

[상위 문서로 돌아가기](./README.md)
