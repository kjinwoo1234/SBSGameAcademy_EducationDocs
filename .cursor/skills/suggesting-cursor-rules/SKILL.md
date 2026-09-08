---
name: suggesting-cursor-rules
description: >
  반복 보정·좌절 신호를 보면 지침 반영을 제안한다. 이 repo는 새 .mdc보다
  보정 루프(기존 project-domain / docs-review / stack-* write)가 기본.
  Trigger: 같은 지적 2회+, "always/never/every time", /suggesting-cursor-rules.
user-invocable: true
---

# Suggesting Cursor Rules (이 repo)

반복 보정 감지 → **먼저 기존 지침에 박기**. 새 `.mdc`는 예외.

## Triggers

- 같은 패턴 보정 **2회+**
- “항상/절대/매번…” 좌절
- 에이전트가 같은 실수 반복

## Decision (이 저장소)

| 상황 | 할 일 |
|------|--------|
| 이미 `project-domain`·`docs-review`·`stack-*`·관련 skill에 **같은 주제** 있음 | **새 파일 금지.** 보정 루프: 문서 전수 수정 + 기존 문장 보강 (`self-update`) |
| 언어 공통 교수법 (조건/반복 입문·뼈대 안내 등) | **`docs-review`만.** `stack-*`·과정별 복제 금지 |
| 엔진·경로 등 진짜 과정 특이점 | 기존 `stack-<과정>.mdc` 또는 globs 오버레이 **1파일** |
| 공통·불변·보안·목차 계약 | `invariant-rules` / `stack-conventions` — 스타일만 invariant에 넣지 않음 |
| 기존에 없고·한 관심사·대상이 ~80줄 넘을 때 | **그때만** 새 `.cursor/rules/<name>.mdc` 제안 |

## How to Suggest

```
같은 보정 [패턴] 반복됨.
기본: 기존 [파일]에 보정 루프로 반영.
새 .mdc는 (조건 충족 시만) 제안 — 승인?
```

승인 전 write 금지. 사용자가 「전부 승인」「커서 지침 업데이트」·보정 재지적이면 `self-update` / 보정 루프대로 **같은 턴 write**.

## New rule shape (예외일 때만)

```markdown
---
description: <한 줄>
globs:
  - <pattern>
alwaysApply: false
---

- <규칙 1>
```

`alwaysApply: true`는 전 세션 필수일 때만. 과정 전용은 globs.

## Rules

- 첫 보정만으로 새 파일 권유 금지
- `.cursor/rules/` 먼저 검색 — **중복 금지**
- 품질 본문 복제 금지 — `docs-review`가 SSOT
- 제안은 짧게. 설교 금지
