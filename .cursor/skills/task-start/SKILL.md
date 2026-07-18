---
name: task-start
description: >-
  Briefing — invariant-rules, docs/목차·구현 확인, 범위 선언. task-start / 작업 시작.
disable-model-invocation: true
---

# Skill: task-start

브리핑 완료 전까지 본문·지침 수정 금지.

## STEP 1 — invariant-rules.mdc

`.cursor/rules/invariant-rules.mdc` 읽기.  
`documentation` — RULE-02·03 N/A. RULE-01·05·06(목차·지침 공개 계약) 적용. RULE-04는 `docs/` vs `.cursor/` 경로만.

## STEP 2 — Locate targets

채팅만 믿지 않는다.

```bash
ls docs/*/README.md README.md .cursor/rules .cursor/skills 2>/dev/null
```

`project-domain.mdc` **구현 상태** 표와 대조.  
`docs/` 작업이면 해당 과정 `README.md` 목차·파일 실존 확인. 품질 기준: `skills/docs-review/SKILL.md` (**선행 문법만** 포함).

## STEP 3 — Scope declaration

```markdown
### 수정할 파일
- path → 챕터 / 과정 README / 루트 README / .mdc / skill

### 확인만
- path → reason (목차 링크, 선행 챕터 용어 등)

### 범위 밖
- …
```

## STEP 4 — Git / lock

심볼 락: 사용자 지시 없으면 N/A. 브랜치만 기록.

## STEP 5 — Start

한 문장 후 **STEP 3 범위 안에서만** 작업.  
챕터형이면 `docs-review` 구조·예시·연습문제 패턴을 염두.  
구조 금지: `## 세부 주제`·`## 실습 체크리스트` (`project-domain`).  
사용자 보정·재지적이면 범위 선언 후 **보정 루프** (`instruction-ecosystem`).
