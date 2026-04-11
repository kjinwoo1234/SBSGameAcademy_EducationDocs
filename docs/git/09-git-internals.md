# Chapter 9 Git 내부 동작 원리

## 학습 목표
- `git add`가 만드는 객체(블롭·트리) 개념을 말로 설명한다.
- 커밋 객체가 부모·트리·메타데이터를 갖는 이유를 이해한다.
- 브랜치가 refs로 구현된다는 점을 안다.

## 세부 주제
- 인덱스(스테이징)와 객체 DB
- 커밋 객체 구조
- `git cat-file`로 객체 엿보기
- refs와 HEAD

## 실습 체크리스트
- 로컬 저장소에서 `git cat-file -p HEAD` 실행 후 출력 필드 읽기
- `.git/refs/heads/main` 파일 내용이 커밋 해시인지 확인

## 본문

<a id="ch9-1"></a>
### 9-1 git add 명령의 동작 원리

- `git add`는 파일 내용을 **해시(blob 객체)**로 `.git/objects`에 저장하고, **인덱스(staging area)**에 "다음 커밋에 넣을 스냅샷"을 기록합니다.
- 동일 내용은 같은 해시로 **중복 저장을 줄입니다**.

---

<a id="ch9-2"></a>
### 9-2 git commit 명령의 동작 원리

- `git commit`은 스테이징된 트리를 가리키는 **tree 객체**와 메타데이터·**부모 커밋 해시**로 **commit 객체**를 만듭니다.
- 브랜치 포인터(예: `main`)가 새 커밋을 가리키도록 갱신됩니다.

---

<a id="ch9-3"></a>
### 9-3 커밋 객체 살펴보기

예시 명령:
```bash
git cat-file -t HEAD
git cat-file -p HEAD
```

예상 결과(형식 예시):
```text
commit
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent 7e2c2f5...
author Name <mail> 1710000000 +0900
committer Name <mail> 1710000000 +0900

Commit message here
```

코드 해석:
- `tree` 줄은 프로젝트 디렉터리 스냅샷 루트를 가리킵니다.
- `parent`는 직전 커밋(첫 커밋은 없을 수 있음).

---

<a id="ch9-4"></a>
### 9-4 브랜치 작업 동작 원리

- 브랜치는 `.git/refs/heads/브랜치명` 파일에 **40자 해시 1줄**을 저장하는 **참조(ref)**입니다.
- **HEAD**는 보통 `ref: refs/heads/main`처럼 **심볼릭 참조**로 현재 브랜치를 가리킵니다.
- 체크아웃은 HEAD를 옮기고 작업 트리를 해당 트리로 맞춥니다.

연습문제:
1. 문제: blob, tree, commit 객체 각각이 담는 정보를 한 줄로 정의하세요.
2. 문제: 브랜치를 새로 만들었을 때 `.git` 아래 무엇이 생기거나 바뀌는지 추론하세요.

정답 포인트:
- Git = **콘텐츠 주소 객체 DB** + **refs**; 커밋은 스냅샷이 아니라 **객체 그래프의 노드**.

---

[상위 문서로 돌아가기](./README.md)
