---
name: solid-review
description: >-
  문서 레이어·SOLID 대응 리뷰. SOLID, 목차, 유지보수, @solid-review.
disable-model-invocation: true
---

# Skill: solid-review

**목표:** 문서 레이어·단일 책임·목차 계약 위반을 찾고 우선순위 제시.

## STEP 1 — 규칙 로드

`solid-principles.mdc`, `stack-conventions.mdc`, `docs-review` 스킬, `project-domain.mdc`.

## STEP 2 — 범위

- 사용자 지정 경로·PR·브랜치.
- 없으면 `git diff` 또는 지정 파일.
- 공개 계약: 과정 `README.md` 목차, 루트 `README.md`, `.cursor/rules`·`skills`.

## STEP 3 — 점검

| 원칙 | 볼 것 |
|------|--------|
| SRP | 챕터에 여러 단원·README에 본문 덤프·지침/학습 문서 혼선 |
| OCP | 새 주제를 기존 챕터에 욱여넣음 vs 파일+링크 확장 |
| LSP | 같은 과정 챕터 템플릿 불일치; 목차형에 챕터 템플릿 강제 |
| ISP | 과정 README에 세부 불릿 과다; 학습자 화면에 `.cursor` 노출 |
| DIP | 학습 문서가 에이전트 경로·내부 리뷰에 의존 |

```bash
ls docs/*/README.md README.md 2>/dev/null
```

본문 품질(용어·예시·연습)은 SOLID보다 `docs-review`(`/review`)가 우선.

## STEP 4 — 보고

```markdown
## SOLID 리뷰 요약
- 범위: …
- 전체: 🔴 N / 🟡 N / 🟢 N
### SRP … ### OCP … ### LSP … ### ISP … ### DIP …
### docs-review (해당 시)
- …
### 권장 다음 단계
1. …
```

## STEP 5 — 수정

- 리뷰만: 보고만.
- 리팩터: 최소 diff; RULE-06(목차)·`docs-review` 패턴 준수.

이슈 없음 → `SOLID 리뷰: 이상 없음 (범위: …)`.
