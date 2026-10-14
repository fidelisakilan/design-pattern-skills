---
name: refactor
description: "Audit code for ad-hoc logic that a GoF pattern would absorb (if-chains on type, scattered constructors, hand-rolled listeners) and report what to refactor. Use when the user asks to clean up, simplify, or refactor an area. Mid-task, when a change adds a third case to such logic, suggest the audit in one line rather than running it."
---

# Refactor toward patterns

Finds code that is already doing a pattern's job by hand and names the pattern it should become. Report only; apply when the user picks an item.

Argument: a path, directory, or "diff" (changes vs the default branch). No argument means the current directory.

**Auto-triggered mid-task** (you noticed the shape while doing something else): do not run the audit. Finish the current task, then add one line naming the shape and the offer, e.g. `calc.py has a 4-branch if-chain on plan_type (Strategy shape). Run /refactor src/pricing to see the refactor.` Run the full procedure only when invoked by name or the user says yes.

## Procedure

Run steps 2 to 5 in a fresh, read-only subagent when the harness has one (Claude Code `Agent`, Codex or OpenCode subagents, otherwise inline). Brief it with the file list and a pointer to `../pattern/catalog.md`. It returns the ranked report. The main agent relays the report and applies only the item the user picks.

1. **Scope.** Resolve the argument to a file list. For "diff", use `git diff --name-only $(git merge-base HEAD origin/HEAD 2>/dev/null || echo main)`.
2. **Scan for shapes.** Each row is a smell and the pattern that absorbs it. Grep for the signal, then read the hit to confirm the shape is real.

   | Shape in code | Signal to grep | Becomes |
   |---|---|---|
   | `switch`/`if-elif` on a type or mode field, repeated in 2+ methods | `switch\s*\(.*(type\|kind\|mode\|state)` , `if .*\.(type\|kind\|mode\|state) ==` | State if the value transitions, Strategy if it is chosen once |
   | `new Concrete…`/direct constructor calls scattered across callers | `new [A-Z]\w+\(` , `[A-Z]\w+\(.*\)` at call sites for the same family | Factory Method, Abstract Factory if families |
   | Constructor with 5+ params, or many setters before first use | `def __init__\(self(, \w+){5,}` , `constructor\((\w+,\s*){5,}` | Builder |
   | Hand-rolled listener arrays and notify loops | `listeners\|subscribers\|callbacks` with a `for` over them | Observer, or the platform event system |
   | Sequence of `if handler.canHandle` or `if not handled` | `canHandle\|handled\s*=\|next_handler` | Chain of Responsibility |
   | One class that imports and orchestrates most of its siblings | import count per file in the area | Mediator, or Facade if callers are external |
   | Subclass per added feature (`LoggedCachedRetryingClient`) | class names with 2+ adjectives | Decorator |
   | `isinstance`/`instanceof` chains inside operations over a hierarchy | `isinstance\(\|instanceof ` | Visitor if hierarchy is stable, polymorphism otherwise |
   | Copy-pasted algorithm skeletons differing in 2 or 3 steps | near-duplicate functions, compare with `diff` | Template Method, or Strategy if composition is preferred in the codebase |
   | Undo stacks of ad-hoc tuples, or operation logs | `undo\|history\|redo` | Command, Memento for state snapshots |

3. **Gate each hit.** Keep it only if all three hold:
   - **Rule of Three**: three or more variants or call sites exist today. Two is a coincidence.
   - **Active**: the area changed in the last 20 commits (`git log --since` or `git log -20 -- <path>`), or the user is about to change it. Dead code earns no refactor.
   - **Cheaper after**: the next variant becomes one new file or class instead of edits in several places.
4. **Read the catalog** for the chosen pattern: `../pattern/catalog.md` (ships with the `pattern` skill; if missing, read `https://refactoring.guru/design-patterns/<kebab-name>`), "skip when" column. If it says skip, drop the item.
5. **Report**, ranked by variants found, highest first. One line per item plus files:

   ```
   1. Strategy  src/pricing/calc.py:40  if-chain on plan_type, 4 branches, touched 6x this month
      -> PricingStrategy interface + 4 classes, calc() picks by plan_type. ~60 lines moved, 0 behaviour change.
   ```
   End with the items dropped by the gate and which gate dropped them, one line each.

Done when every file in scope has been scanned against every row, and every reported item names the pattern, the files, the variant count, and the size.

## Applying

When the user picks an item: refactor only that item, preserve behaviour, run the existing tests, and keep it in its own commit separate from any feature work.
