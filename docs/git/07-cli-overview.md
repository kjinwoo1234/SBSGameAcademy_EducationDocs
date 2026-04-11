# Chapter 7 CLI 환경에서 Git 명령어 살펴보기

## 학습 목표
- CLI를 쓰는 이유를 실무 관점에서 설명한다.
- Git Bash를 열고 경로 이동·저장소 상태를 확인한다.
- 기본 명령과 원격 관련 명령을 구분해 사용한다.

## 세부 주제
- CLI 선택 이유
- Git Bash 기초
- status / add / commit / log / diff
- remote / fetch / pull / push

## 실습 체크리스트
- Git Bash에서 새 저장소를 만들고 커밋까지 CLI만으로 수행
- `git remote -v`, `git fetch`, `git push` 순서 설명 가능

## 본문

<a id="ch7-1"></a>
### 7-1 왜 CLI를 사용할까?

약어 설명(CLI):
- **CLI**는 **Command Line Interface**의 약자입니다.
- 한국어로는 "명령줄 인터페이스"이며, 텍스트 명령으로 프로그램을 조작하는 방식입니다.
- 스크립트 자동화, 서버 SSH 환경, 상세 옵션 제어에 GUI보다 유리한 경우가 많습니다.

| GUI | CLI |
|-----|-----|
| 시각적·입문에 쉬움 | 로그·옵션·자동화에 강함 |
| 화면마다 다름 | 명령어는 도구 간 동일 개념 |

---

<a id="ch7-2"></a>
### 7-2 Git Bash 시작하기

- Windows에서는 **Git for Windows**에 포함된 **Git Bash**가 흔합니다.
- `pwd`, `ls`, `cd 폴더`로 위치를 옮깁니다. 경로에 공백이 있으면 따옴표로 감쌉니다.

예시 명령:
```bash
cd "/c/work/my-repo"
pwd
```

---

<a id="ch7-3"></a>
### 7-3 기본 Git 명령어 살펴보기

예시 명령:
```bash
git status
git diff
git add -p
git commit -m "message"
git log --oneline --graph --decorate -n 10
```

코드 해석:
- `diff`는 스테이징 전/후 차이를 볼 때 씁니다.
- `add -p`는 변경 일부만 골라 스테이징할 때 유용합니다.

---

<a id="ch7-4"></a>
### 7-4 원격 저장소 관련 Git 명령어 살펴보기

예시 명령:
```bash
git remote -v
git fetch origin
git pull origin main
git push origin main
```

용어 설명:
| 용어 | 의미 |
|------|------|
| fetch | 원격 갱신 정보를 가져오기(로컬 브랜치는 즉시 안 바뀔 수 있음) |
| pull | fetch + 현재 브랜치에 통합(설정에 따라 merge/rebase) |

연습문제:
1. 문제: `fetch`만 했을 때 `git log origin/main`과 `git log main`이 달라질 수 있는 이유를 설명하세요.
2. 문제: `git push`가 rejected될 때 가장 먼저 의심할 두 가지 원인을 쓰세요.

정답 포인트:
- CLI = 자동화·정밀 제어; 원격 = `remote` 등록 + `fetch`/`pull`/`push` 흐름 구분.

---

[상위 문서로 돌아가기](./README.md)
