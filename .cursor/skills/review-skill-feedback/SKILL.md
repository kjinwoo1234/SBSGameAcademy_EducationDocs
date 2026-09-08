---
name: review-skill-feedback
description: >
  사용자가 /review(docs-review) 또는 /review-improve 산출물(총점·채점표·개선안·문서 diff)을
  평가하면, 그 피드백을 일반화해 docs-review / review-improve 스킬을 같은 턴에 보강한다.
  Use whenever the user says /review-skill-feedback, /calibrate-review, "리뷰 평가",
  "채점이 틀렸어", "개선이 이상해", "리뷰 스킬 고쳐", "review feedback", or pastes
  judgment on an AI review/improve run and wants the review skills themselves updated—
  not just one docs/ file fixed. Prefer this over plain @self-update when the complaint
  is about scoring criteria, improve loop behavior, or false 100s. Do not use for
  ordinary chapter edits without process feedback.
disable-model-invocation: true
---

# review-skill-feedback — 리뷰·개선 공정 피드백 → 스킬 보강

**목표:** AI가 돌린 `/review`·`/review-improve` 결과를 **사람이 평가**하고, 그 평가를 **재발 방지 규칙**으로 `docs-review` / `review-improve`에 심는다.  
**왜:** 문서 한 장만 고치면 같은 채점·개선 실수가 다음 장에서 반복된다. 공정(스킬)을 고쳐야 다음 `/review`부터 행동이 바뀐다.  
**하지 말 것:** 피드백 없이 스킬 임의 편집; 평가를 “문서 한 줄 수정”으로만 끝내고 스킬 미반영; 새 `.mdc`를 기본으로 만들기(품질 SSOT는 `docs-review`).

관련: `docs-review`(채점 SSOT) · `review-improve`(반영 루프) · `self-update`(보정 write) · `suggesting-cursor-rules`(반복 보정 제안).

---

## STEP 0 — 입력 확인

다음을 모은다. 부족하면 **한 번만** 짧게 되묻는다.

| 항목 | 출처 예 |
|------|---------|
| **대상 공정** | `/review`만 / `/review-improve`만 / 둘 다 |
| **산출물** | `.result/review-….md`, `.result/improve-….md`, 해당 `docs/…` |
| **사람 평가** | 채팅 지적, “총점 과장”, “Q2 날림”, “개선안이 문서와 무관” 등 |
| **기대** | 스킬만 고침(기본) / 스킬+해당 문서 재리뷰까지 |

기본값:

- 스킬 write = **예** (이 스킬 호출 = 공정 보정 승인으로 본다. RULE-05·`self-update` 보정 루프와 동일 계열)
- `docs/` 재수정 = **사용자가 문서도 고치라고 할 때만**
- 새 `.mdc` = **금지**. 품질 규칙 → `docs-review`; 루프 동작 → `review-improve`

---

## STEP 1 — 산출물·평가 파싱

1. 리뷰/improve 파일을 연다. 없으면 사용자가 가리킨 경로·최근 `.result/review-docs-*`를 찾는다.
2. 사람 평가를 **현상**으로 정규화한다 (예/추측 분리).

| 평가 유형 (예) | 의미 |
|----------------|------|
| **점수 인플레** | 아니오여야 할 ID를 예로 줌. 총점 100이 허수 |
| **기준 오해** | Q2를 직전·직후만 봄, F5 선행 위반 놓침 등 |
| **개선 과잉/과소** | improve가 리뷰에 없는 리팩터 / 개선안 미적용 |
| **형식 붕괴** | 채점표·개선안·improve 기록 누락·날림 |
| **범위 이탈** | 다른 과정 맞춤, 목차 무단 변경, 대상 밖 수정 |
| **좋은 점** | 유지할 패턴 (스킬에 “하지 말 것”으로 지우지 말 것) |

3. `.result/feedback-review-skill-<YYYYMMDD-HHmm>.md`에 원문 요약을 남긴다 (다음 반복·감사 추적).

---

## STEP 2 — 원인 → 스킬 패치 위치

피드백 한 건마다 **어느 스킬의 어느 STEP/표**인지 고른다.

| 원인 | 쓸 곳 | 쓰는 내용 |
|------|--------|-----------|
| 채점 ID·렌즈·장 범위·선행·예시/연습 패턴 | `skills/docs-review/SKILL.md` | 기준 문장·실패 예·Q2/F5 주의·금지 |
| 100 루프·개선 우선순위·외과 diff·정체/상한 | `skills/review-improve/SKILL.md` | STEP 보강. **채점 본문 복제 금지** — docs-review 포인터 |
| repo 지도·금지 섹션 최소표만 | `project-domain.mdc` | 품질 장문 복제 금지 |
| 생태계 진입(언제 이 스킬 쓰는지) | `instruction-ecosystem.mdc` | 한 줄 포인터만 |

**원칙:** 같은 주제가 `docs-review`에 있으면 **그쪽을 보강**한다. `review-improve`에는 “docs-review를 느슨하게 적용하지 말 것” 같은 **공정 가드**만 추가한다.

애매하면 `docs-review` 쪽을 기본으로 한다 (SSOT).

---

## STEP 3 — 패치 설계 (일반화)

문서 한 파일에만 맞는 예외 문구를 스킬에 넣지 않는다.

1. 사람 평가에서 **재현 가능한 실패 조건**을 한 문장으로 뽑는다.  
   예: “총점 100인데 `### 연습문제`에 입력/출력/조건이 없으면 P2는 아니오여야 한다.”
2. 기존 스킬에 **이미 비슷한 문장**이 있는지 grep한다. 있으면  sharpen(더 구체적 실패 예)만. 없으면 최소 단락 추가.
3. `ALWAYS`/`NEVER` 남발 대신 **왜 학습·채점이 망가지는지**를 한 줄 넣는다.
4. 필요 시 `docs-review` 채점표 근거 열에 넣을 **한 줄 실패 예**를 추가한다.

분기:

- 문서도 틀렸고 스킬도 느슨함 → **스킬 먼저** 고친 뒤, 사용자 요청 시 해당 문서에 `/review` 또는 `/review-improve` 재실행을 제안(또는 명시 시 실행).
- 스킬은 맞는데 이번 실행만 실수 → 스킬에 “자기점검 체크” 한 줄 + 해당 문서만 재리뷰(요청 시).

---

## STEP 4 — Write (같은 턴)

1. `docs-review` / `review-improve` (해당 시 `instruction-ecosystem` 포인터)를 수정한다.
2. `self-update` 출력 형식에 맞춰 요약을 보고한다.
3. feedback 기록 파일에 **반영한 스킬 경로·추가 문장 요지**를 追記한다.

**금지:** 피드백과 무관한 스킬 대청소; 학습 `docs/`에 `.cursor` 경로 노출; 평가를 무시하고 “이미 100이라 OK”만 반복.

---

## STEP 5 — 검증·재실행 제안

1. 방금 넣은 규칙으로 **지적된 산출물**을 다시 읽었을 때, 같은 실수를 **아니오/개선안**으로 잡을 수 있는지 스스로 점검한다.
2. 채팅에 제안:
   - `같은 파일에 /review 재실행해서 새 기준 적용해 줄까?`
   - improve 이슈면 `/review-improve` 재실행 제안
3. 사용자가 동의하면 해당 공정만 재실행한다 (이 스킬이 문서 루프 전체를 자동으로 다시 돌리지 않는다 — 명시 요청 시만).

---

## 사람 평가 입력 형식 (권장)

자유롭게 써도 되고, 아래면 파싱이 정확하다.

```markdown
대상: docs/git/00-quickstart.md
공정: review-improve
문제:
- 총점 100인데 연습문제에 조건이 없음 (P2)
- Q2 범위 대조가 직전 장만 봄
기대: docs-review 기준 강화. 문서도 다시 채점
```

---

## 보고 형식

```markdown
## /review-skill-feedback 결과

### 사람 평가 요약
- …

### 원인 → 패치
| 평가 | 스킬 | 변경 요지 |
|------|------|-----------|
| … | docs-review / review-improve | … |

### 반영한 파일
- `skills/docs-review/SKILL.md`
- `skills/review-improve/SKILL.md` (해당 시)

### 기록
- `.result/feedback-review-skill-….md`

### 다음
- [ ] 동의하면 대상에 `/review` 또는 `/review-improve` 재실행
```

---

## 경계

| 상황 | 이 스킬 | 다른 스킬 |
|------|---------|-----------|
| 문서 내용만 틀렸고 채점 기준은 동의 | 문서 수정은 `/review-improve` 또는 수동 | 스킬 피드백 아님 |
| 같은 문서 보정 반복·지침 일반 | 보정 루프 + `self-update` | 겹치면 품질→docs-review 우선 |
| 새 규칙 파일 만들고 싶음 | 기본 거절 → docs-review 보강 | `suggesting-cursor-rules` |
| review 없이 스킬만 이론 수정 | 산출물·평가 없으면 되물음 | |

---

## 성공 기준

- 사람 평가가 **스킬 문장 1개 이상**으로 남고, 다음 유사 `/review`에서 같은 구멍을 막는다.
- `docs-review`가 품질 SSOT로 유지되고 `review-improve`에 채점 본문이 복제되지 않는다.
- feedback 기록이 `.result/`에 남아 추적 가능하다.
