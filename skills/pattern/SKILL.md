---
name: pattern
description: "Run before planning or starting a feature, module, or non-trivial change. Surveys the codebase for existing GoF patterns, picks the pattern that fits the new work (refactoring.guru catalog), and says whether to reuse, introduce, or refactor toward it. Also use when the user names a pattern or asks which pattern fits. For auditing existing code, use /refactor instead."
---

# Design patterns

Pattern tables with intent, "reach for when" and "skip when" live in [catalog.md](catalog.md). Read it at step 3. A pattern is a named solution to a recurring design problem, not a goal. The question is always "which problem?" first, "which pattern?" second.

## Procedure

Steps 2 to 4 run in a fresh, read-only subagent when the harness has one (Claude Code `Agent`, Codex or OpenCode subagents, otherwise inline). Brief it with the problem sentence from step 1, the feature area, and a pointer to `catalog.md`. It returns the Design block below. The main agent follows that block without re-deriving it. A fresh agent sees the codebase without the plan already in its head, so its pick is not anchored on yours.

1. **Name the problem.** One sentence, no pattern names. If you cannot, there is no pattern to apply; build the feature plainly and stop here.
2. **Survey the codebase.** Find patterns already in use so the new work matches the house style instead of inventing a second way:
   ```bash
   # names that advertise a pattern
   grep -rEn --include='*.{py,ts,tsx,js,go,rs,java,kt,cs,rb,dart}' \
     -e '(Factory|Builder|Strategy|Observer|Adapter|Decorator|Facade|Proxy|Command|Mediator|Visitor|Handler|Repository|Provider)\b' . | grep -vE 'node_modules|vendor|dist|build' | head -40
   # interfaces / abstract bases with more than one implementation (the real signal)
   grep -rEn --include='*.{py,ts,tsx,go,java,kt,cs}' -e '^\s*(export )?(abstract class|interface|class \w+\(ABC\)|type \w+ interface)' . | grep -vE 'node_modules|vendor|dist|build' | head -40
   ```
   Then read the two or three hits closest to the feature area. Record: pattern, where, how many variants.
3. **Match.** Read `catalog.md` and map the problem sentence to a row using "reach for when", not the name. Check the ladder first: an idiom, stdlib type, or an abstraction found in step 2 that already solves it wins.
4. **Decide reuse, introduce, or refactor:**
   - **Reuse**: the pattern exists in the codebase and fits. Add the new variant to it. Say where.
   - **Introduce**: the pattern fits and nothing like it exists. Add the smallest version: one interface, the variants that exist today, no speculative ones.
   - **Refactor**: the feature area already does this job the ad-hoc way (a growing `if/elif` on type, duplicated construction, hand-rolled callbacks) and the new work would add a third case. Recommend the refactor as a separate step before the feature, name the pattern and the files, and size it. Do not bundle it into the feature diff.
   - **None**: the problem matches no row in the catalog. Say so and build plainly.
5. **Name it in code** only if the name helps the reader (`PaymentStrategy` yes, `AbstractSingletonProxyFactoryBean` no).

Done when the plan states: problem sentence, patterns found in the codebase, chosen row or "none", the decision from step 4 with file paths, and why simpler rungs failed.

Output shape at the top of the plan:

```
Design: <problem sentence>
Existing: <pattern> in <path> (<n> variants) | none found
Fit: <pattern> | none
Decision: reuse <path> | introduce <pattern> at <path> | refactor <path> to <pattern> first (~<size>)
```
