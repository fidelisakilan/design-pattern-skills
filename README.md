# design-pattern-skills

**Two agent skills that pick, reuse, or refactor toward GoF design patterns.**

Built on the [refactoring.guru catalog](https://refactoring.guru/design-patterns). `pattern` fires when you plan a feature and says which pattern fits, or that none does. `refactor` fires when you ask to clean up and names the pattern your ad-hoc code is already imitating.

## Install

Works with Claude Code, Codex, OpenCode and anything else the [skills CLI](https://skills.sh) supports.

```sh
npx skills add fidelisakilan/design-pattern-skills -a '*' -g
```

Drop `-g` to install into the current project only. Install both: `refactor` reads the catalog shipped with `pattern`.

## Skills

| Skill | Fires when | Returns |
|-------|-----------|---------|
| `pattern` | planning a feature, or a pattern is named | `Design / Existing / Fit / Decision` block: reuse, introduce, refactor first, or none |
| `refactor <path\|diff>` | "clean up", "simplify", or a change adds a third case to an if-chain | ranked list of ad-hoc code and the pattern that absorbs it |

Both run on their own or by name. Mid-task, `refactor` only suggests itself in one line; it audits when you say so.

## How it works

- **Problem first.** Each run starts by naming the problem in one sentence with no pattern names. No sentence, no pattern.
- **Codebase survey** finds patterns already in use so new work matches the house style.
- **Fresh subagent** does the survey where the harness has one, so the pick is not anchored on the main agent's plan.
- **Rule of Three.** Fewer than three variants or call sites means no refactor.
- **Catalog** is one file, `skills/pattern/catalog.md`: 22 patterns with intent, reach for when, skip when.

## Tweaking

Edit `skills/pattern/catalog.md` to change what counts as a fit, and the smell table in `skills/refactor/SKILL.md` to change what the audit looks for. Thresholds like the Rule of Three live next to the step that uses them.

## Uninstall

```sh
npx skills remove pattern refactor -a '*'
```

## Credits

Pattern descriptions paraphrased from [refactoring.guru](https://refactoring.guru) by Alexander Shvets.
