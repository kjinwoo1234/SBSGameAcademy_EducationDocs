---
name: caveman-help
description: >
  Quick-reference card for all caveman modes, skills, and commands.
  One-shot display, not a persistent mode. Trigger: /caveman-help,
  "caveman help", "what caveman commands", "how do I use caveman".
---

# Caveman Help

Display this reference card when invoked. One-shot — do NOT change mode, write flag files, or persist anything. Output in caveman style.

## Modes

| Mode | Trigger | What change |
|------|---------|-------------|
| **Lite** | `/caveman lite` | Drop filler. Keep sentence structure. |
| **Full** | `/caveman` | Drop articles, filler, pleasantries, hedging. Fragments OK. Default. |
| **Ultra** | `/caveman ultra` | Extreme compression. Bare fragments. Tables over prose. |
| **Wenyan-Lite** | `/caveman wenyan-lite` | Classical Chinese style, light compression. |
| **Wenyan-Full** | `/caveman wenyan` | Full 文言文. Maximum classical terseness. |
| **Wenyan-Ultra** | `/caveman wenyan-ultra` | Extreme. Ancient scholar on a budget. |

Mode stick until changed or session end.

## Skills

| Skill | Trigger | What it do |
|-------|---------|-----------|
| **caveman-commit** | `/caveman-commit` | Terse commit messages. Conventional Commits. ≤50 char subject. |
| **docs-review** | `/review` | 품질 SSOT. 100점 + 앞·뒤 장 **전부** 대조 (Q2). |
| **review-improve** | `/review-improve` | `.result` 개선안 → re-`/review` until **100** (max 5). |
| **self-update** | `@self-update` / 보정 재지적 | 지침 write. 보정이면 유사 전수. |
| **cavecrew** | delegate / 압축 위임 | Task `explore`/`generalPurpose` + 압축 계약. 유령 타입 없음. |
| **suggesting-cursor-rules** | 같은 보정 2회+ | 기존 지침 보강 우선. 새 `.mdc`는 예외. |
| **caveman-compress** | `/caveman-compress <file>` | Compress .md memory files. |
| **caveman-help** | `/caveman-help` | This card. |

## Correction loop

User says “already told you” / leftover forbidden section / same mistake again → same turn: fix all + update rule/skill. No “I’ll be careful next time.” See `instruction-ecosystem` 보정 루프.

## Deactivate

Say "stop caveman" or "normal mode". Resume anytime with `/caveman`.

## Configure Default Mode

Default mode = `full`. Change it:

**Environment variable** (highest priority):
```bash
export CAVEMAN_DEFAULT_MODE=ultra
```

**Config file** (`~/.config/caveman/config.json`):
```json
{ "defaultMode": "lite" }
```

Set `"off"` to disable auto-activation on session start. User can still activate manually with `/caveman`.

Resolution: env var > config file > `full`.

## More

Full docs: https://github.com/JuliusBrussee/caveman
