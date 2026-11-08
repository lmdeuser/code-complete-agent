# Chapter 26: Code-Tuning Techniques

[← Previous: Chapter 25](25-Chapter-25-Code-Tuning-Strategies.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 27 →](27-Chapter-27-How-Program-Size-Affects-Construction.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Once a verified performance bottleneck has been isolated with a profiler, and high-level architectural and algorithmic options have been exhausted, developers may turn to specific **Code-Tuning Techniques**.

These techniques involve surgical adjustments to logic, loops, data structures, and mathematical expressions. Steve McConnell insists that **code-tuning techniques are highly compiler- and hardware-dependent**. A micro-optimization that was effective in 1993 or on C++ may degrade performance on modern pipelined CPUs or in JIT-compiled runtimes (Java/V8). Always measure the specific impact.

---

## 2. Theoretical Foundations & Key Concepts

### 1. Logic Tuning
- **Stop Testing When You Know the Answer**: Rely on short-circuit evaluation (`&&`, `||`) or return immediately once the condition is satisfied.
- **Order Tests by Frequency**: In an `if-else` or `switch` chain, place the most common cases at the top to minimize average comparison counts.
- **Replace Complex Expressions with Table Lookups**: Table lookups can outperform multi-branch evaluation and improve clarity, but measure performance in the actual runtime.

### 2. Loop Tuning
- **Unswitching**: If a loop contains a conditional whose condition does not change during loop execution, move the `if` *outside* the loop:
  - *Bad*: `for (i = 0; i < N; i++) { if (mode == FAST) fast(); else slow(); }`
  - *Good*: `if (mode == FAST) { for (i = 0; i < N; i++) fast(); } else { for (i = 0; i < N; i++) slow(); }`
- **Jamming (Combining Loops)**: Combine two adjacent loops over the same index space into a single loop to reduce loop overhead and improve CPU cache locality.
- **Minimizing Work Inside Loops**: Pre-compute invariants, string lengths, or array lookups outside the loop header.

### 3. Data Transformations
- **Caching**: Cache computationally expensive or I/O-intensive results.
- **Integer Math Instead of Floats**: Use integer arithmetic where fractional accuracy is not required.
- **Structure of Arrays (SoA) vs. Array of Structures (AoS)**: For SIMD vectorization and cache locality in performance-critical data processing.

### 4. Expression Tuning
- **Precompute Results at Compile Time / Startup**: Replace complex mathematical evaluations with constant lookup tables.
- **Strength Reduction**: Replace expensive operations with cheaper ones (e.g. replacing exponentiation with multiplication).

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 26.1 — Hoist Loop Invariants**: In measured hot paths, consider hoisting invariant calculations when the compiler does not already do so and readability is preserved.
2. **RULE 26.2 — Unswitch Invariant Conditionals**: Consider moving invariant condition checks outside measured high-frequency loops after correctness and baseline checks.
3. **RULE 26.3 — Exploit Short-Circuiting**: Order boolean evaluation terms so that the cheapest and most likely-to-fail term executes first in `AND` operations, and cheapest/most likely-to-succeed executes first in `OR` operations.
4. **RULE 26.4 — Preserve Verification Tests**: Retain the original, readable implementation as a benchmark and verification baseline in test fixtures.

---

## 4. Operational Checklist Derived from McConnell: Code-Tuning Techniques

- [ ] Have loop invariants been hoisted outside loop bodies?
- [ ] Have loops over identical index ranges been evaluated for jamming?
- [ ] Are invariant conditionals unswitched outside high-volume loops?
- [ ] Are boolean terms ordered to optimize short-circuit evaluation?
- [ ] Have expensive operations inside critical loops been replaced via strength reduction?
- [ ] Has the tuned code been empirically benchmarked against the original?

---

## 5. Heuristics & Metrics

- **Benchmark Precision**: Use warm-up, repeated samples, variance, and representative workloads; iteration counts depend on operation cost and runtime.
- **Regression Threshold**: Compare measured benefit against the performance requirement and readability cost; no universal 10% rule applies.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Loop-Invariant Overhead and Re-computation
```javascript
// BAD: Re-evaluating invariant properties and regex inside high-frequency loop
function filterRecords(records, prefix) {
  const result = [];
  for (let i = 0; i < records.length; i++) {
    // Invariant recomputation inside loop: regex compilation and uppercase conversion!
    if (new RegExp('^' + prefix.toUpperCase()).test(records[i].code.toUpperCase())) {
      result.push(records[i]);
    }
  }
  return result;
}
```

### Pattern: Hoisted Invariants and Optimized Matching
```typescript
// GOOD: Invariants hoisted outside loop, clean string prefix matching
export interface RecordItem {
  readonly code: string;
}

export function filterRecords(
  records: readonly RecordItem[],
  prefix: string
): RecordItem[] {
  // Hoist invariant preparations outside the loop
  const normalizedPrefix = prefix.toUpperCase();
  const result: RecordItem[] = [];

  for (let i = 0, len = records.length; i < len; i++) {
    const item = records[i];
    if (item.code.toUpperCase().startsWith(normalizedPrefix)) {
      result.push(item);
    }
  }

  return result;
}
```

---

## 7. Inspection & Verification Protocol

1. Audit high-frequency loops: verify that no invariant expressions are evaluated inside.
2. Verify that micro-tuned code has explicit comments explaining why the optimization was applied, referencing profiler metrics.
