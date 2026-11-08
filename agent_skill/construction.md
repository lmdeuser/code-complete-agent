# Clear and Defensive Construction

Derived primarily from *Code Complete*, chapters 7–19 and 31–32. Follow the language idioms and formatter already used by the project.

## Routines, variables, and types

- Keep routines cohesive and names honest about return values and significant side effects.
- Declare and initialize variables near first use where local style permits. Reduce scope and live time when that clarifies data flow.
- Prefer problem-domain names. Conventional short names are valid in small scopes; do not enforce a character-count gate.
- Extract a literal only when its meaning, ownership, or duplication benefits from a name. Self-evident values need no ceremonial constants.
- Use exact representations and explicit rounding for money. Handle units, encoding, overflow, truncation, and precision where they affect correctness.
- Use tolerance-based float comparison for approximate calculated values, with domain-appropriate absolute and relative tolerances. Exact comparison is valid for sentinels, zero checks, and intentionally exact values.

## Errors and assertions

- Validate untrusted data at real system boundaries and use the project's validated domain types afterward.
- Use assertions for programmer invariants, without required side effects. Do not rely on assertions alone for expected runtime failures.
- Handle anticipated errors through the project's error mechanism. Catch exceptions only to handle them or add useful context; never hide failures silently.
- Neutral values, retries, fallback data, result types, exceptions, and fail-fast behavior each have valid contexts. Choose according to the contract and failure risk.
- Do not log the same exception repeatedly at every layer or expose secrets and sensitive payloads in diagnostics.

## Control flow and comments

- Use guard clauses, positive conditions, iterators, and table-driven dispatch when they make the actual code clearer.
- Treat deep nesting and high decision counts as review signals, not automatic defects.
- Bound retries, external waits, and uncertain convergence with timeout, cancellation, or iteration limits. Naturally terminating loops need no artificial counter.
- Match existing line width, braces, indentation, import order, and blank-line conventions. Never reformat a whole project to enforce this reference.
- Comments may explain why, intent, contracts, assumptions, and useful high-level what. Remove only redundant or stale comments, not valuable interface or algorithm documentation.

## Construction review

- Are observable behavior and side effects clear from interfaces?
- Are external boundaries validated without noisy repeated checks everywhere?
- Are numeric and error semantics appropriate to the domain?
- Does the code fit surrounding idioms without unrelated cleanup?
- Did affected comments and docstrings remain accurate?
