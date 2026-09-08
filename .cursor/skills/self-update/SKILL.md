---
name: self-update
description: >-
  .cursor/rules/*.mdc·관련 skills에 팀 지식 반영. @self-update·지침 업데이트·
  사용자 보정/재지적 시 같은 턴 write. 보정 루프면 유사 사례 전수 수정도 포함.
  self-update, 지식 반영, 지침 업데이트, 보정 루프.
disable-model-invocation: true
---

# Skill: self-update

팀 지식은 **`.cursor/rules/*.mdc`** (+ 해당 시 `skills/*/SKILL.md`). `.cursor/` 아래 에이전트용 `.md` 신규 금지.

## Classification

| Layer | Path | 내용 |
|-------|------|------|
| Universal | (드묾) | 여러 repo·언어 공통 |
| Domain | `project-domain.mdc` | 레이아웃·공개 목차·최소 구조 (품질 본문 복제 금지) |
| Domain quality | `skills/docs-review/SKILL.md` | **품질 SSOT** — 따라가기·채점 |
| Course overlay | `rules/stack-*.mdc` | 엔진·경로 등 특이점만 (입문 교수법 금지 — docs-review) |
| Improve | `skills/review-improve/SKILL.md` | 리뷰 반영 + 총점 100 (패턴은 docs-review 참조) |
| Review calibrate | `skills/review-skill-feedback/SKILL.md` | 사람 평가로 docs-review/review-improve 보강 |
| Immutable | `invariant-rules.mdc` | 비밀·목차 계약·지침 |
| Conventions | `stack-conventions.mdc` | 문서 레이어 |
| Structure | `solid-principles.mdc` | 문서 SOLID 대응 |
| Ephemeral | `agent-local.mdc` | 세션 (gitignore·없으면 무시) |
| Process | `instruction-ecosystem.mdc` | 생태계·**보정 루프** |
| Suggest rule | `skills/suggesting-cursor-rules` | 반복 보정 → 기존 보강 우선·새 .mdc는 예외 |

스타일·문장만 → invariant 금지. `AGENTS.md` 루트 중복 금지.

## Triggers (언제 write)

| 트리거 | 지침 write | 유사 사례 전수 |
|--------|------------|----------------|
| `@self-update` / 「self-update」 | 예 | 해당 시 |
| 「커서 지침 업데이트」 | 예 | 해당 시 |
| **사용자 보정/재지적** (이미 말한 요청 반복) | **예 (필수)** | **예 (필수)** |

보정 루프 상세: `instruction-ecosystem.mdc` 「사용자 보정 루프」.

## Process (일반)

1. `.cursor/rules/`·관련 skill에 없는 사실 나열.
2. 대상 `.mdc` / `SKILL.md` 선택; ~80줄 넘으면 분리·오버레이 추가 검토.
3. **why** 중심으로 변경 요약을 짧게 보고한 뒤, **같은 턴에 바로 반영**한다.
4. 별도 「승인」 대기를 하지 않는다.
5. 반영할 지식이 없으면 write 하지 않고 `반영할 팀 지식 없음.`만 보고한다.

## Process (보정 루프)

사용자가 실수·누락을 재지적하면:

1. **즉시 수정** — 지적된 파일/현상.
2. **유사 전수** — 같은 패턴 grep (과정·`docs/`·`.cursor`). 전부 수정. 잔여 0 확인.
3. **지침 write** — 재발 방지 문장을 최소 1곳. 품질 → **`docs-review` 먼저**(SSOT). 과정 전용 → `stack-*`. repo 지도만 → `project-domain`. 개선 공정 → `review-improve`는 체크 포인터만.
4. **보고** — 고친 건수 + 지침 경로. 「다음에 조심」만 금지.

목표: 사용자가 **같은 요청을 다시 하지 않아도** 되게.

## Forbidden

- 위 Triggers **없이** rules/skills를 임의 편집
- 보정인데 한 파일만 고치고 지침 안 고침
- `.cursor/` 에이전트용 `.md` 신규
- invariant에 스타일·문장만 넣기

## Output

```markdown
## /self-update 결과
### 새 지식
- …
### 유사 사례 (보정 시)
- N건 수정 / 잔여 0
### 반영한 변경
**대상:** `.cursor/rules/….mdc` 또는 `skills/…/SKILL.md`
- (적용한 내용 요약 1~5줄)
**이유:** …
```

없으면 `반영할 팀 지식 없음.`
