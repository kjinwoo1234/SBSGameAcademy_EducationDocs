# cavecrew

메인 컨텍스트 절약용 **위임 가이드**. 별도 에이전트 타입 없음.

## What it does

Cursor `Task`의 **가용** `subagent_type`(`explore`, `generalPurpose` 등)에 **압축 출력 계약**을 프롬프트로 붙인다.  
유령 이름(`cavecrew-investigator` / `builder` / `reviewer`) **사용 금지**.

| 역할 | Task 타입 | 계약 |
|------|-----------|------|
| 조사 | `explore` | path:line 목록 |
| 1~2파일 수정 | `generalPurpose` | path:range + verified |
| 빠른 리뷰 | `generalPurpose` | findings only |
| 교육 문서 채점 | 메인 + `/review` | cavecrew 아님 |

## See also

- [`SKILL.md`](./SKILL.md) — 계약 전문
- `rules/communication-style.mdc` — 짧은 판단표
