---
name: student-brain-loop
description: >
  생초보 student-brain 학습과 학습자료 개선을 한 루프로 돌린다. 막히면
  Stop/need로 docs를 고친다. 문단만 쌓지 말고 소제목·챕터 분리·새 장 추가를
  우선 검토한다(한 소절에 새 개념·문법·함수가 둘 이상이면 분리). 직전 brain
  유지 후 수정 문서부터 재독. 범위 완주까지 반복. Use whenever the user says
  /student-brain-loop, "학습 개선 루프", "brain loop", "student-brain 루프",
  "막히면 고치고 다시 읽기", "수정 문서부터 다시 읽기", or wants student-brain
  plus doc fixes in one closed loop until completed_to_target. Prefer this over
  plain /student-brain when improvement and re-read must continue without
  waiting for「계속」. Prefer this over /review for beginner-passable docs via
  simulation, not a scorecard.
---

# student-brain-loop — 학습→개선→재학습 루프

**목표:** 지정 **리뷰 범위**의 학습 자료를 생초보가 **이해하며 끝까지** 통과할 때까지, `student-brain` 학습과 `docs/` 개선을 자동 반복한다.  
**종료 조건(유일):** 범위 안 **모든** 학습 단위를 순서대로 읽었고, 뇌 로그 `status: completed_to_target`이며 `## Stop` 막힘이 없다.  
**하지 말 것:** `/review` 채점표로 종료 판정, 막힌 채 다음 장 건너뛰기, 사전 지식으로 용어 메우기, 범위 밖 과정 “맞춰” 수정, 사용자「계속」대기하며 루프 멈추기(이 스킬은 자동 재개), 개선 후 **범위 첫 문서부터** 통째로 다시 읽기.

## 형제 스킬

| 스킬 | 이 루프에서의 역할 |
|---|---|
| `student-brain` | **읽기 엔진.** 페르소나·읽기 단위·뇌 로그 경로·Stop 규칙을 **그대로** 따른다. 루프마다 SKILL을 열고 실행한다. |
| `docs-review` / `review-improve` | **기본 루프에 넣지 않는다.** 사용자가 점수 루프를 따로 시키면 그때만. |
| `project-domain` | 비대화·**한 소절 다개념** 시 **구조 이관·소절/챕터 분리·새 장** (`개선 시 비대화 → 구조`). |

`student-brain`만 호출하면 `docs/`를 고치지 않는다. **이 스킬**이 개선 권한을 연다.

## STEP 0 — 리뷰 범위 확정

사용자가 범위를 주었는지 확인한다.

| 입력 예 | 해석 |
|---|---|
| `docs/c/01-intro.md` | 그 과정 목차 **첫 장 → 해당 파일** |
| `docs/c` / “C 과정 전체” | 그 과정 README 목차 **첫 장 → 마지막 장** |
| 커리큘럼 언리얼 경로 (C→C++→UE) | 각 과정 README 순서대로 **연결한 전체 목록**의 끝 문서까지 |
| 경로 없음 | **한 번만** 되묻는다. 추측 금지. |

확정 후 기록:

- `scope_list`: 읽을 챕터 경로 목록 (순서 고정)
- `target`: 목록 **마지막** 경로
- `학습범위` / 뇌 로그 파일: `student-brain` 파일명 규칙 준수  
  - 단일 과정: `.result/student-brain(<과정>-to-<목표 stem>).md`  
  - 다중 과정: 최종 목표 과정·stem 기준 (예: `unrealEngine-to-16-server-model-gameflow`). Session notes에 전체 `scope_list`를 적는다.

## STEP 1 — 한 바퀴 (학습)

1. `.cursor/skills/student-brain/SKILL.md`를 연다.  
2. 페르소나·읽기 단위·산출 골격을 **그대로** 적용한다.  
3. 읽기 시작점:
   - **최초** (또는 사용자가 「처음부터」명시): `scope_list` **맨 앞**부터.
   - **개선 직후 재학습:** STEP 2의 `resume_from` 문서부터. 그 **직전**까지의 `## Learned so far` / `## Reading log`는 **유지**.
4. 뇌 로그를 해당 `student-brain(<학습범위>).md`에 쓴다 (재학습 시 앞장 로그를 지우지 않는다).  
5. 분기:
   - `stopped_confused` → STEP 2  
   - `completed_to_target` 이고 읽은 파일이 `scope_list` 전체와 맞음 → STEP 4 종료  
   - `completed_to_target` 인데 목록 일부만 읽음 → 로그·범위를 재점검 (보통은 target=목록 끝이면 한 번에 끝까지)

채팅은 `student-brain`과 같이 **짧은 정상 한국어**. 루프 중에는 「계속」을 묻지 않고 다음 STEP으로 간다. 진행만 한두 문장 보고.

## STEP 2 — 개선 (막힘만)

근거는 뇌 로그 **`## Stop`** 과 **`need`** 뿐이다. 채점표·외부 커리큘럼으로 필수 목록을 만들지 않는다.

1. `file` / `location` / `unknown`에 해당하는 `docs/`만 연다.  
2. 생초보가 그 지점을 지나갈 수 있게 고친다.  
3. **문단만 덧대지 말고 구조를 먼저 검토한다 (필수).** 아래 중 하나라도 해당하면 **소제목 추가·소절 분리·새 챕터 추가·제목 재명명**을 문단 누적보다 우선한다.  
   | 신호 | 검토할 조치 |
   |---|---|
   | 소절(`###`) **하나**에 **서로 다른 새 개념·문법·함수·API가 둘 이상** 한꺼번에 도입됨 | 소절을 쪼개거나, 역할이 크면 **새 챕터**로 분리 |
   | 설명이 길게 이어져 **읽기 힘들거나 지루한 서술**만 늘어남 | 소제목으로 단계를 나누고, 예제·표·따라하기를 붙임. 같은 말 반복 문단은 줄임 |
   | 장 제목·학습 목표가 가리키는 역할과 본문이 어긋남 | 제목·목표를 맞추거나, 다른 역할 내용은 **이관·새 장** |
   | 같은 장에 풀이·예시를 또 얹으면 장이 비대해짐 / 뒤 장과 역할 겹침 | **이관·챕터 분리·소절 축소** (`project-domain` 「개선 시 비대화 → 구조」) |
   **금지:** “일단 한 문단 더”만으로 막힘을 메우고 구조 검토를 건너뛰기.  
4. 목차·파일명·번호가 바뀌면 과정 `README.md`를 함께 맞춘다 (RULE-06). 새 챕터·제목 변경 시 `scope_list`도 갱신한다.  
5. `docs/` 밖(`.cursor/`)은 이 루프의 기본 대상이 아니다. 사용자가 지침 반영을 따로 시키면 그때.

### 재학습 시작점 (`resume_from`)

개선 후 기록하고 STEP 1로 돌아간다.

| 항목 | 규칙 |
|---|---|
| `resume_from` | 손댄 파일 중 `scope_list`에서 **가장 앞**인 문서 (보통 `## Stop`의 `file`) |
| 유지 | `resume_from` **직전** 문서까지 Learned / Reading log |
| 폐기·덮어쓰기 | `resume_from` 문서부터의 Reading log 조각, `## Stop`, 그 이후 장 로그(아직 없으면 해당 없음) |
| 다시 읽기 | `resume_from` **파일 맨 앞**부터 문장·문단 단위 (고친 소절만 이어 읽지 않음) |
| 금지 | 범위 **첫 장**부터 통째 재독 / 앞장 brain 삭제 후 재작성 |

같은 학습범위 파일을 갱신하되, 앞장 이해는 다시 쌓지 않는다.

## STEP 3 — 정체·안전장치

자동 반복이 기본이다. 다만 다음에 해당하면 **사용자에게 짧게 묻고 대기**한다.

| 상황 | 행동 |
|---|---|
| 같은 `file`+`location`(또는 같은 `unknown`)이 **연속 2회** Stop | 개선이 안 먹힌 것. 원인 가설 한 줄 + 선택지(더 큰 구조 변경 / 범위 축소 / 중단) |
| 한 세션에서 개선 사이클이 과도하게 김 (대략 **15회**+) | 진행 요약 후 계속 여부 확인 |
| 범위·목차가 모호해짐 (파일 이동 후 목록 붕괴) | `scope_list` 재확정 |

정체가 아니면 「계속」을 기다리지 않는다.

## STEP 4 — 종료

종료는 아래를 **모두** 만족할 때만이다.

1. 뇌 로그 `status: completed_to_target`  
2. Reading log가 `scope_list`의 **모든** 챕터를 순서대로 다룬다 (중간에 Stop으로 비어 있지 않음)  
3. `## Stop` 막힘 섹션 없음 (또는 “해당 없음”)  
4. 채팅: 범위 완주 보고 + 뇌 로그 경로 + 이번 루프에서 손댄 `docs/` 요약(파일 목록 수준)

**예외 — 구조 밀도 follow-up:** 치명 막힘 없이 완주했어도, 뇌 로그 **Density notes**나 Reading log `partial`에 **한 소절 다개념·읽기 힘듦**이 명시되어 있으면 STEP 2 구조 표에 따라 **소절 분리·새 장·제목**을 적용한다. 그다음 `resume_from`부터 재학습해, 같은 밀도 신호가 줄었는지 확인한 뒤 STEP 4로 돌아온다. 문단만 늘리는 follow-up은 하지 않는다.

미완주인데 사용자가 중지를 명하면 `status`를 로그에 남기고 중단한다. 종료 조건 달성으로 치지 않는다.

## 채팅 문체

- `student-brain`과 동일: **짧고 정상적인 한국어**, 텔레그램식 비문 금지.  
- 상세 생각·Stop 이유는 **파일**. 채팅은 사이클 상태·경로·고친 파일만.  
- 루프 중 예:  
  > `docs/c/03-variables-operators.md`에서 막혀 해당 소절을 고친 뒤, 직전 장까지 brain을 유지한 채 03부터 다시 읽습니다.  
- 완주 예:  
  > 리뷰 범위(…→…)를 이해하며 끝까지 읽었습니다.  
  > 로그: `.result/student-brain(…).md`  
  > 수정: `docs/...` …

## 작업 순서 요약

```
범위 확정 → student-brain(최초는 맨 앞부터)
    ├─ 막힘 → Stop 근거로 docs 개선(문단만 X → 소제목·챕터 분리·새 장 우선)
    │         → 직전 문서까지 brain 유지
    │         → 수정 문서(resume_from)부터 다시 읽기
    ├─ 정체/과다 사이클 → 사용자 확인
    └─ 범위 전체 completed_to_target → 종료
```

## 금지

- 막힌 용어를 모델 지식으로 정의하고 학습만 진행  
- 개선 없이 같은 Stop에서 「일단 다음 장」  
- 막힘을 **문단만 추가**로 메우고, 소제목 분리·새 챕터·제목 수정을 검토하지 않음  
- 개선 후 **범위 첫 문서부터** 통째로 다시 읽기 (직전 brain 폐기)  
- 개선 후 고친 소절만 건너뛰고 **다음 장**으로 진행 (수정 문서는 파일 從頭 재독)  
- `/review` 총점으로 루프 종료  
- 뇌 로그 없이 docs만 고치고 끝  
- 고정명 `.result/student-brain.md` 덮어쓰기  
- 학습 로그를 `docs/`에 기록  
