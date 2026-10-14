# design-pattern-skills

Two agent skills built on the [refactoring.guru design pattern catalog](https://refactoring.guru/design-patterns).

| Skill | Fires when | Does |
|---|---|---|
| `pattern` | You plan a feature, or name a pattern | Surveys the codebase for patterns already in use, picks the one that fits, and says reuse / introduce / refactor first / none. |
| `refactor` | You ask to clean up an area, or a change adds a third case to an if-chain | Audits code for ad-hoc logic a pattern would absorb and reports a ranked list. Applies one item when you pick it. |

Both can be auto-triggered by the agent or run by name (`/pattern`, `/refactor <path|diff>`).

## Install

```bash
# all agents you have (Claude Code, Codex, OpenCode, Cursor, ...)
npx skills add fidelisakilan/design-pattern-skills -a '*' -g

# or pick agents
npx skills add fidelisakilan/design-pattern-skills -a claude-code -a codex -a opencode -g
```

Drop `-g` to install per project instead of globally.

## Layout

```
skills/
  pattern/
    SKILL.md      procedure: name problem, survey codebase, match, decide
    catalog.md    22 patterns: intent, reach for when, skip when
  refactor/
    SKILL.md      smell -> pattern table, Rule of Three gate, report format
```

`refactor` reads `../pattern/catalog.md`, so install both.

## License

MIT. Pattern descriptions are paraphrased from refactoring.guru, which is the place to go for the full write-ups.
