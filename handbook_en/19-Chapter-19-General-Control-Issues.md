# Chapter 19: General Control Issues

[← Previous: Chapter 18](18-Chapter-18-Table-Driven-Methods.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 20 →](20-Chapter-20-The-Software-Quality-Landscape.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Control flow is the circulatory system of software. When control structures are abused, cyclomatic complexity explodes, and human engineers as well as AI agents lose the ability to reason about program states.

Thomas McCabe's **Cyclomatic Complexity** is a useful quantitative review metric for control flow. Values above 10 often justify closer inspection and testing, while project conventions determine block delimiters and formatting.

---

## 2. Theoretical Foundations & Key Concepts

### 1. Boolean Expressions
- **Evaluate in Natural Order**: Put conditions in the order they occur in the real world.
- **Parenthesize for Clarity**: Never rely on subtle operator precedence rules (`a && b || c && d`). Explicitly use parentheses: `((a && b) || (c && d))`.
- **Short-Circuit Evaluation**: Rely on short-circuiting to protect subsequent expressions from runtime errors (e.g. `if (ptr != null && ptr->isValid())`).

### 2. Compound Statements (Blocks)
- **Always Use Braces `{}`**: Even for single-line statements! Omitting braces leads to catastrophic bugs (e.g. Apple's infamous `goto fail;` SSL defect).

### 3. Taming Complexity: Cyclomatic Complexity Metric
Thomas McCabe's metric $v(G)$ measures the number of linearly independent paths through a routine:
$$v(G) = E - N + 2P$$
In practice, calculate $v(G)$ by starting with $1$ and adding $1$ for every:
- `if`, `elif`
- `while`, `for`
- `case` branch
- `&&`, `||` boolean operator
- `catch` block

**Complexity Tiers**:
- **1–5**: Simple, easily understood, low defect risk.
- **6–10**: Moderate complexity; fully acceptable.
- **11–20**: High complexity; refactoring recommended.
- **\> 20**: High-risk control flow that merits focused review and testing; not literally untestable.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 19.1 — Complexity Review $v(G)$**: Treat complexity above 10 as a signal to inspect cohesion, readability, and test coverage rather than an automatic rejection.
2. **RULE 19.2 — Explicit Block Structure**: Follow project brace and indentation conventions, preferring explicit blocks where they prevent misleading control flow.
3. **RULE 19.3 — Parenthesize Compound Booleans**: Always parenthesize mixed boolean operations (`&&` mixed with `||`).
4. **RULE 19.4 — Positive Boolean Phrasing**: Phrase boolean logic positively. Invert negative comparisons to eliminate double negatives.

---

## 4. Operational Checklist Derived from McConnell: General Control Issues

- [ ] Are routines with elevated cyclomatic complexity reviewed for readability and adequate tests?
- [ ] Are all compound statements enclosed in explicit braces `{}`?
- [ ] Are mixed boolean expressions parenthesized to prevent precedence bugs?
- [ ] Does the code exploit short-circuit evaluation safely?
- [ ] Are conditions phrased positively?
- [ ] Are null statements explicit and documented?

---

## 5. Heuristics & Metrics

- **McCabe Cyclomatic Metric**: Values above 10 are a review signal, not a universal acceptance gate.
- **Boolean Term Count**: Conditions with many terms are candidates for intermediate booleans or helper routines when that improves clarity; no fixed term count is a defect.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: High Complexity, Missing Braces, Ambiguous Precedence
```c
// BAD: Missing braces, operator precedence trap, high complexity
if (status == 1 && flag || override && !disabled)
    doFirstAction();
    doSecondAction(); // BUG: Always executes! Misleading indentation without braces
```

### Pattern: Low Complexity, Explicit Braces, Parenthesized Booleans
```cpp
// GOOD: Explicit braces, parenthesized boolean logic, low complexity
bool isEligibleForOverride(int status, bool isFlagActive, bool isOverrideRequested, bool isSystemActive) {
    bool isStandardAuthorized = (status == 1) && isFlagActive;
    bool isEmergencyAuthorized = isOverrideRequested && isSystemActive;

    return isStandardAuthorized || isEmergencyAuthorized;
}

void executeAuthorizedActions(int status, bool flag, bool override, bool active) {
    if (isEligibleForOverride(status, flag, override, active)) {
        doFirstAction();
        doSecondAction();
    }
}
```

---

## 7. Inspection & Verification Protocol

1. Calculate cyclomatic complexity where useful: review routines above project thresholds for cohesion and test coverage.
2. Verify all single-line `if`/`for` blocks use explicit braces.
3. Ensure boolean conditions with $> 2$ terms have parentheses.
