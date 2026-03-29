# C 파일 입출력

파일 입출력은 프로그램 데이터를 디스크에 저장/복원하는 기술입니다.  
콘솔 프로그램을 "실사용 가능한 프로그램"으로 만드는 핵심 단계입니다.

## 1) 파일 열기 모드
- `"r"`: 읽기 전용 (파일이 없으면 실패)
- `"w"`: 쓰기 전용 (기존 내용 덮어쓰기)
- `"a"`: 이어쓰기 (파일 끝에 추가)
- `"rb"`, `"wb"`: 바이너리 모드

## 2) 기본 흐름
1. `fopen`으로 파일 열기
2. 읽기/쓰기 수행
3. `fclose`로 닫기

```c
#include <stdio.h>

FILE* fp = fopen("score.txt", "w");
if (fp == NULL) {
    printf("파일 열기 실패\n");
    return;
}

fprintf(fp, "name=%s score=%d\n", "Hong", 100);
fclose(fp);
```

## 3) 파일 읽기
```c
FILE* fp = fopen("score.txt", "r");
if (fp != NULL) {
    char name[20];
    int score;
    fscanf(fp, "name=%19s score=%d", name, &score);
    printf("%s %d\n", name, score);
    fclose(fp);
}
```

## 4) 한 줄 단위 처리
- 읽기: `fgets`
- 쓰기: `fputs`

```c
char line[100];
FILE* fp = fopen("log.txt", "r");
if (fp != NULL) {
    while (fgets(line, sizeof(line), fp) != NULL) {
        printf("%s", line);
    }
    fclose(fp);
}
```

## 5) 실수 방지 체크리스트
- 파일 포인터 `NULL` 검사 여부
- 읽기 포맷과 데이터 형식 일치 여부
- 모든 성공 경로/실패 경로에서 `fclose` 보장 여부

## 6) 연습 문제
1. 사용자 이름/점수를 파일에 저장하고 다시 읽어 출력
2. 로그 파일에 실행 시간 한 줄씩 누적 저장(`a` 모드)
3. 학생 3명의 정보를 파일에 저장 후 평균 점수 계산

## 관련 브릿지 학습
- [디버깅 워크플로우 기초](../common/01-debugging-workflow.md)
- [버전 관리와 협업 기초](../common/02-version-control-collaboration.md)

---

[상위 문서로 돌아가기](./README.md)
