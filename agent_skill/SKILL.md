---
name: code-complete
description: Applies context-aware software-construction guidance derived from Code Complete for design, implementation, testing, debugging, refactoring, and performance work. Use only when explicitly invoked as /code-complete; project instructions and conventions always take precedence.
disable-model-invocation: true
---

# Code Complete Construction Guidance

Use this skill as an optional engineering checklist, not as a global style policy. It is an unofficial adaptation of Steve McConnell's *Code Complete*, 2nd Edition, plus modern agent-safety practices.

## Authority and blast radius

1. Follow system, developer, user, and applicable project instructions before this skill.
2. Read the target project's own guidance, architecture, manifests, source, and tests. Existing conventions win over every heuristic here.
3. Stay within the requested change. Do not reformat unrelated files, refactor surrounding code, add dependencies, install tools, alter global agent settings, commit, push, or access external systems without authorization.
4. Numeric thresholds are diagnostic heuristics, never automatic rejection criteria unless the current project explicitly adopts them.
5. Mocks, stubs, fixtures, scaffolding, and TODOs are acceptable when intentional and appropriate to the authorized task. Report unfinished production behavior honestly.
6. Do not claim an unexecuted check passed. Separate changes, verification, external effects, and remaining uncertainty.

## Workflow

1. **Understand**: identify the requested behavior, acceptance evidence, compatibility constraints, and relevant standard project commands.
2. **Design**: choose the simplest cohesive design; hide volatile details and preserve existing dependency boundaries without speculative abstraction.
3. **Construct**: use honest names, explicit data flow, appropriate boundary validation, expected-error handling, and comments that add useful intent or structure.
4. **Verify**: run relevant real project checks and the user's scenario. Test requirements and meaningful boundaries rather than pursuing a universal metric.
5. **Improve separately**: keep refactoring behavior-preserving; require representative before/after measurements for optimization.
6. **Hand off**: review the final diff and generated files; report verified results and unverified limitations precisely.

## Load only the relevant reference

- Designing prerequisites, architecture, classes, or pseudocode: read [design.md](design.md).
- Implementing routines, variables, data types, control flow, layout, or comments: read [construction.md](construction.md).
- Reviewing, testing, or debugging: read [verification.md](verification.md).
- Refactoring, optimizing, integrating, or selecting tools: read [evolution.md](evolution.md).
- Evaluating a numeric threshold or metric: read [heuristics.md](heuristics.md).

Read one or two references as needed, not all of them by default. When a rule seems to conflict with project practice, verify the project rule and follow it. If the task is simple, use only the workflow above.

## Final check

- Correct requested behavior and explicit acceptance evidence.
- Existing architecture, idioms, style, and toolchain preserved.
- No speculative abstractions or unrelated improvements.
- Errors and boundaries handled according to actual risk.
- Relevant tests and real execution completed where possible.
- No unapproved environment or external side effects.
- Remaining limitations stated plainly.
