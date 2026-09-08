---
name: cavecrew
description: >
  메인 컨텍스트 절약용 위임. Cursor Task 가용 타입(explore/generalPurpose 등)에
  압축 출력 계약을 붙여 spawn. 유령 타입(cavecrew-investigator 등) 없음.
  Trigger: delegate, cavecrew, 압축 위임, save context.
---

# Cavecrew — 압축 위임 (가용 Task만)

**사실:** Cursor `Task`의 `subagent_type`은 제품이 노출한 목록만 유효.  
이 스킬은 **별도 에이전트 타입을 만들지 않는다.** `explore` / `generalPurpose` 등에 **출력 계약을 프롬프트로 강제**한다.

가짜 이름 `cavecrew-investigator` · `cavecrew-builder` · `cavecrew-reviewer` → **사용 금지.**

## When to delegate

| Task | `subagent_type` | 프롬프트에 붙일 계약 |
|------|-----------------|----------------------|
| 위치·심볼·링크 찾기만 | `explore` | **조사 계약** |
| 조사 + 짧은 구조 코멘트 | `explore` | 조사 계약 + “코멘트 ≤3줄” |
| 명확 1~2파일 수정 | `generalPurpose` | **빌드 계약** + 경로·변경 요지 |
| 과정 횡단 3파일+ | 메인 스레드 | 위임 최소화 |
| diff·지침 빠른 점검 | `generalPurpose` | **리뷰 계약** |
| 교육 문서 따라가기 채점 | 메인 + `docs-review` (`/review`) | cavecrew 아님 |
| 이미 아는 한 줄 | 메인 | 위임 없음 |

**요약:** 도구 결과가 메인 컨텍스트에 그대로 들어가니, 긴 산문 대신 아래 계약을 프롬프트 맨 위에 넣는다.

## Output contracts (프롬프트에 복붙)

**조사** (`explore`)
```
출력 형식만 사용. 산문 금지.
<Header>:
- path:line — `symbol` — short note
totals: <counts>.
없으면: No match.
```

**빌드** (`generalPurpose`, ≤2 files)
```
출력 형식만:
<path:line-range> — <change ≤10 words>.
verified: <re-read OK | mismatch @ path:line>.
범위 초과면 첫 토큰: too-big. / needs-confirm. / ambiguous.
```

**리뷰** (`generalPurpose`)
```
path:line: <severity>: <problem>. <fix>.
totals: N critical / N warn / N info.
없으면: No issues.
전반적 감상·아키텍처 에세이 금지.
```

## Chaining

1. 조사(`explore`) → 2. 메인이 1~2곳 고름 → 3. 빌드 → 4. 리뷰  
병렬: 각도 다른 `explore` 2~3개 한 메시지.

## What NOT to do

- 존재하지 않는 `subagent_type` 이름 사용
- 파일 모를 때 빌드만 spawn
- 5파일 리팩터를 빌드 계약으로 위임
- 사람이 읽을 최종 답을 서브에이전트 압축 출력 그대로 붙임 → 메인이 caveman으로 재서술
- `/review` 대신 cavecrew로 교육 문서 100점 채점

## 관계

짧은 판단표: `rules/communication-style.mdc` (포인터). 상세·계약은 **이 스킬**.
