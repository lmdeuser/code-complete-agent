# CODE COMPLETE: OPERATIONAL GUIDANCE FOR AI CODING AGENTS

> **Source**: An unofficial operational adaptation of Steve McConnell's *Code Complete*, 2nd Edition, with clearly separated modern agent-safety practices.
> **Purpose**: Optional guidance for explicit use during software construction. This is not a mandatory global protocol and not a replacement for project documentation.

## 1. Authority, Scope, and Side Effects

1. Follow the active instruction hierarchy. System, developer, user, and applicable project instructions take precedence over this document.
2. Discover and follow the target project's architecture, conventions, toolchain, and definition of done. Do not replace them with preferences from this handbook.
3. Apply changes only within the requested scope. Do not reformat unrelated files, refactor surrounding code, add dependencies, change global agent configuration, install tools, commit, push, or access external systems unless authorized.
4. Use the project's existing test and scaffolding conventions. Mocks, stubs, fixtures, and deliberate placeholders are legitimate when they serve an authorized task; unfinished production behavior must be reported rather than disguised.
5. Treat numeric thresholds as review heuristics, not universal pass/fail gates. A project may adopt its own enforced limits explicitly.

## 2. Prime Directives

1. **Manage complexity**: Prefer cohesive modules, narrow interfaces, explicit data flow, information hiding, and code that is easy to reason about.
2. **Work from evidence**: Read the relevant code and configuration before changing them. Distinguish verified facts, assumptions, and unresolved questions.
3. **Proportion rigor to risk**: A scratch script, a library, and a safety-critical service need different amounts of design and verification. Avoid both under-engineering and speculative abstraction.
4. **Preserve behavior unless change is requested**: Do not mix feature work, bug fixes, refactoring, formatting, and optimization merely because the same files are open.
5. **Report honestly**: Never claim that an unexecuted check passed. Separate source changes, verification results, external side effects, and remaining limitations.

## 3. Construction Workflow

### Phase 1 — Understand and Define Acceptance

1. Read the applicable project instructions, relevant source, manifests, and existing tests before implementation.
2. Identify the requested behavior, important edge cases, compatibility constraints, and the standard commands that should verify the change.
3. Ask about ambiguity only when it materially changes the result and cannot be resolved from the project. Otherwise choose the smallest reasonable interpretation and disclose it when useful.
4. Do not invent missing APIs, libraries, architecture layers, requirements, benchmark results, or tool output.

### Phase 2 — Design Only What Is Needed

1. Ask what implementation details each module should hide. Keep volatile formats, protocols, and third-party details behind suitable interfaces when that reduces real coupling.
2. Prefer high cohesion and loose coupling. Use inheritance only for genuine substitutability; otherwise prefer composition or simpler procedural design.
3. Preserve existing architectural strata and dependency direction. Do not introduce a new layered architecture into an established project without authorization.
4. For non-trivial control flow, optionally sketch intent-level pseudocode or desk-check the algorithm. Retain only comments that remain useful; do not leave mechanical pseudocode noise.
5. Avoid speculative generalization. Solve the concrete requirement with the simplest design that satisfies known constraints.

### Phase 3 — Implement Clearly and Safely

#### Routines and classes

1. Give each routine and class a clear, cohesive purpose. A name should describe observable behavior and significant side effects honestly.
2. Keep interfaces as small as practical. Group parameters into a domain object only when they form a coherent concept and the grouping improves the call site.
3. Do not enforce an arbitrary routine-length limit. Decompose when cohesion, nesting, decision count, data flow, or independent testability suffers.
4. Preserve established mutability conventions. Prefer immutable values where they simplify reasoning, but do not convert an existing design dogmatically.

#### Variables and data

1. Declare and initialize variables close to first use when the language and local style permit. Keep scope, span, and live time small enough to make data flow clear.
2. Use names that communicate the problem-domain concept. Short names are acceptable for small, conventional scopes; name length is not an independent quality gate.
3. Replace literals when their meaning, ownership, or repetition makes them unclear. Do not create named constants for self-evident values merely to satisfy a count.
4. Use exact decimal, integer minor units, or another project-approved representation for monetary values, including an explicit rounding policy where required.
5. Use approximate floating-point comparison for calculated values where representation error matters, with relative and absolute tolerances appropriate to the domain. Exact comparison remains valid for sentinels, exact-zero checks, and intentionally exact representations.
6. Handle encoding, overflow, truncation, units, timezone, and precision explicitly when they affect correctness.

#### Errors and control flow

1. Validate untrusted input at system boundaries and convert it into the project's established validated types. Avoid repeated defensive checks that add noise without protecting a real boundary.
2. Use assertions for programmer invariants where the project supports them. Keep assertions free of required side effects and do not use them as the sole handling for expected runtime failures.
3. Handle anticipated failures with the project's error mechanism. Catch only exceptions the code can meaningfully handle or add context to; do not silently discard failures.
4. Prefer readable nominal paths and guard clauses when they reduce nesting. Deep nesting and many decision points are review signals, not automatic defects.
5. Add explicit retry, timeout, cancellation, or iteration bounds when waiting on external events or convergence that may never occur. Do not add meaningless counters to naturally terminating loops.
6. Use table-driven dispatch when it simplifies repetitive, data-like branches; keep ordinary conditional logic when it is clearer.

#### Comments and layout

1. Match the file's indentation, line width, brace style, import order, and formatter configuration. Never impose a universal 80/100-column rule.
2. Write comments that add intent, rationale, assumptions, contracts, or useful high-level structure. Remove comments that merely transliterate syntax or contradict the code.
3. Update comments and docstrings affected by a behavior change. Do not delete valuable documentation solely because it describes *what* an interface or algorithm does.

### Phase 4 — Verify and Debug

1. Run the project's standard build, test, typecheck, lint, and formatting checks that cover the change, insofar as they exist and are appropriate to the task. Do not enable a new strictness regime globally without authorization.
2. Add tests only within the project's established testing practice. Cover requirements, meaningful boundaries, invalid data, and regression risk rather than chasing a universal coverage percentage.
3. When using structured basis testing, treat cyclomatic complexity as a minimum test-count aid, not proof that arbitrary `$v(G)$` tests cover every path or data variation.
4. For debugging, stabilize or precisely characterize the failure, form a hypothesis, test it, fix the confirmed cause, and verify the result. Do not apply random changes until symptoms disappear.
5. Where feasible, demonstrate that a regression check fails before a bug fix and passes afterward. Search for similar defects when useful, but do not fix unrelated issues without authorization.
6. Exercise the user's real scenario end to end, not only imports or compilation. State plainly what could not be executed.

### Phase 5 — Refactor and Optimize Deliberately

1. Keep refactoring behavior-preserving, incremental, and separate from functional changes unless the requested design explicitly requires otherwise.
2. Use existing tests as a safety net when available. Their absence is a risk to disclose and address proportionally, not a universal prohibition on every requested change.
3. Do not optimize on intuition alone. Establish a realistic performance requirement, measure a representative workload, change the smallest relevant area, and measure again.
4. Prefer requirements, architecture, algorithms, and data structures over micro-tuning. Preserve a correctness baseline and reject a tuning change whose measured benefit does not justify its complexity.
5. Treat historical performance ratios and example gains as context, never as promised results on current hardware or runtimes.

### Phase 6 — Integrate and Hand Off

1. Integrate in small, verifiable increments when the task spans multiple components. Run relevant integration or smoke checks after wiring them together.
2. If a relevant pre-existing check fails, determine whether the change caused it. Do not hide failures or fix unrelated breakage silently.
3. Review the final diff for scope, generated artifacts, secrets, accidental files, and documentation drift.
4. Report what changed, which commands were run and their results, any external effects, and any remaining unverified behavior. Commit or push only when requested.

## 4. Heuristics, Not Automatic Gates

| Topic | Responsible use |
|---|---|
| Routine size | Review cohesion and complexity. McConnell allows some cohesive algorithms to reach 100–200 lines and advises caution beyond 200; this is not a target length. |
| Parameters | Around seven is a cognitive warning sign from the book. Smaller team limits may be useful, but coherent domain objects are preferable to mechanical splitting. |
| Cyclomatic complexity | Values above 10 commonly justify review and additional tests. Complexity alone does not prove that code is defective or untestable. |
| Nesting | More than roughly three levels often hurts readability. Refactor only when simplification improves the actual code. |
| Variable names | The book cites useful average ranges, but short conventional names and longer domain names remain valid. |
| Coverage | Branch, requirement, boundary, and data coverage matter more than a universal percentage. |
| Performance | Profile the current system. Historical 80/20 and 4%/50% observations are not service-level objectives. |

## 5. Final Review Checklist

- [ ] Was the requested behavior implemented within the authorized scope?
- [ ] Are project instructions, architecture, conventions, and toolchain respected?
- [ ] Are interfaces honest, cohesive, and no more abstract than needed?
- [ ] Are boundary validation, errors, numeric precision, and side effects handled appropriately?
- [ ] Are comments and documentation accurate after the change?
- [ ] Did the relevant real checks run, and are their results reported accurately?
- [ ] Were unrelated changes, generated artifacts, and global environment changes avoided?
- [ ] Are remaining assumptions, failures, and unverified scenarios stated explicitly?

## 6. Source Map

- Chapters 1–5: construction, prerequisites, design, complexity, and information hiding.
- Chapters 6–9: classes, routines, defensive programming, and pseudocode.
- Chapters 10–19: variables, data types, statements, control flow, and tables.
- Chapters 20–23: quality, collaboration, testing, and debugging.
- Chapters 24–26: refactoring and measured performance work.
- Chapters 27–32: scale, integration, tools, layout, and documentation.
- Chapters 33–35: humility, craftsmanship, and continued learning.

Modern agent-safety additions in this document include explicit scope limits, environment side-effect controls, provenance of verification claims, and opt-in use. They are operational extensions, not quotations or official checklists from the book.
