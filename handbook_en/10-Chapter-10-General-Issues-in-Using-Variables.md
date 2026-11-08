# Chapter 10: General Issues in Using Variables

[← Previous: Chapter 9](09-Chapter-09-The-Pseudocode-Programming-Process.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 11 →](11-Chapter-11-The-Power-of-Variable-Names.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Variables are the vessels that store the state of your application. Most coding bugs—uninitialized memory, race conditions, accidental overwrites, stale values—stem from poor variable discipline. Steve McConnell establishes three foundational metrics for variable health: **Scope, Span, and Live Time**.

The supreme dogma is the **Principle of Proximity**: *Declare, initialize, and use variables as close together as possible*. Long variable live times and sprawling scopes exponentially increase the vulnerability window during which bugs can be introduced.

---

## 2. Theoretical Foundations & Key Concepts

### Key Metrics of Variable Usage
1. **Variable Scope**: The region of code in which a variable is visible and accessible.
   - *Rule*: Keep scope as narrow as humanly possible (local block > local routine > class member > module > global).
2. **Variable Span**: The average number of lines of code between references to a variable.
   - Low span means references are clustered together. High span means the reader must scan up and down hundreds of lines to trace data flow.
3. **Variable Live Time**: The total number of statements between the variable's first reference and its last reference.
   - Long live times make code difficult to refactor and increase the risk that unrelated code will inadvertently modify state.

### Explicit Initialization
- Initialize variables at the time of declaration.
- Check compiler/linter warnings for uninitialized variables.
- Re-initialize loop accumulators immediately prior to the loop header, never at the top of the enclosing routine.

### Single-Purpose Rule
- **Give every variable a clear purpose**.
- Avoid reusing a variable for unrelated conceptual values when that obscures data flow. Ordinary accumulator and loop-variable updates remain valid idioms.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 10.1 — Proximity of Declaration and Use**: Declare variables near first use where the language and local style permit; preserve required legacy declaration patterns.
2. **RULE 10.2 — Minimize Live Time**: Keep live time short enough that data flow remains clear; treat long spans as review signals rather than numeric defects.
3. **RULE 10.3 — Avoid Confusing Variable Reuse**: Do not reuse one variable for unrelated conceptual values when separate names would improve clarity.
4. **RULE 10.4 — Initialize Before Use**: Ensure every variable is initialized before reading it, following language-specific declaration idioms.
5. **RULE 10.5 — Favor Useful Immutability (`const`, `final`, `readonly`, `val`)**: Prefer immutable declarations where they clarify contracts and fit project conventions.

---

## 4. Operational Checklist Derived from McConnell: Using Variables

- [ ] Are variables declared and initialized close to where they are first used?
- [ ] Is every variable initialized explicitly before it is read?
- [ ] Are variable scopes as small as possible (block-level where applicable)?
- [ ] Is the span between variable references minimized?
- [ ] Is variable live time short enough to keep data flow understandable?
- [ ] Does each variable have one and only one distinct purpose?
- [ ] Are loop control variables initialized immediately before the loop?
- [ ] Are variables marked immutable (`const`/`final`) by default?

---

## 5. Heuristics & Metrics

- **Span Calculation**:
  $$\text{Span} = \frac{\sum \text{lines between references}}{\text{number of references} - 1}$$
  Use span as a diagnostic measure; no universal line threshold applies to every routine.
- **Live Time Window**: Live time should ideally fit on a single screen without scrolling.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: High Span, Premature Declaration, Reused Temporary Variable
```javascript
// BAD: Declarations far from use, sprawling live time, variable reuse
function computeMetrics(records) {
  let temp; // Reused for multiple unrelated calculations!
  let i;    // C89 style premature loop index
  let totalScore = 0;

  // 40 lines of unrelated validation logic...

  temp = getAdjustmentFactor();
  totalScore += temp;

  // 30 lines of logging and network pings...

  for (i = 0; i < records.length; i++) {
    temp = records[i].score * 1.5; // Reused! Overwriting previous concept
    totalScore += temp;
  }
  return totalScore;
}
```

### Pattern: Minimal Span, Immediate Initialization, Strict Immutability
```typescript
// GOOD: Minimal live time, const immutability, single responsibility
export interface ScoreRecord {
  readonly score: number;
}

export function computeTotalScore(
  records: readonly ScoreRecord[],
  adjustmentFactor: number
): number {
  // Pure, localized computation with zero variable reuse
  const recordSubtotal = records.reduce((sum, r) => sum + r.score * 1.5, 0);
  const totalScore = recordSubtotal + adjustmentFactor;
  return totalScore;
}
```

---

## 7. Inspection & Verification Protocol

1. Trace every variable from declaration to final use: verify live time is minimized.
2. Check that no variable is reassigned to represent a different semantic entity.
3. Verify all variables are initialized before use, following language-specific declaration idioms.
