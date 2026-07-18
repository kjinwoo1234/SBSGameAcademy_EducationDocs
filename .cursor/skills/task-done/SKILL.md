---
name: task-done
description: >-
  Closure — diff, RULE self-check, docs-review, optional self-update. task-done.
disable-model-invocation: true
---

# Skill: task-done

완료 표시 전 실행.

## STEP 1 — Change summary

의도 vs `git diff`; 의도 밖 변경 나열.

## STEP 2 — Self-check (`invariant-rules.mdc`)

프로젝트에 해당하는 항목만 체크:

- [ ] RULE-01 secrets
- [ ] RULE-02 N/A (런타임 없음) — 해당 시만
- [ ] RULE-03 N/A
- [ ] RULE-04 `docs/`·`.cursor/` 경로 혼동 없음
- [ ] RULE-06 챕터/순서 변경 시 과정 README(+ 필요 시 루트 README) 동기화; 지침 변경 시 `.cursor/` 정합
- [ ] `docs/` 변경 시 `docs-review` 패턴 (유형 구분·placeholder·링크·따라가기)
- [ ] 구조·레이어 이슈 시 `solid-principles.mdc` / `@solid-review`

## STEP 3 — Branch / lock

브랜치 기록; 락 해제.

## STEP 4 — Worktree

사용자 worktree 사용 시만.

## STEP 5 — self-update?

재사용 패턴·도메인 사실 → `@self-update` (대상 `.mdc` / 관련 skill).

이번 작업 중 **사용자 보정·재지적**이 있었으면 보정 루프가 끝났는지 확인:

- [ ] 유사 사례 잔여 0
- [ ] 재발 방지 지침이 `.mdc` 또는 skill에 반영됨
- 미완이면 완료 처리 금지 → 보정 루프 먼저.

## STEP 6 — Report

짧게: 완료 / blocked / human 필요.

RULE 위반·미선언 변경 시 완료 처리 금지.
