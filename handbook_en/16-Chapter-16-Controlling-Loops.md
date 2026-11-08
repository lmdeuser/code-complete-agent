# Chapter 16: Controlling Loops

[← Previous: Chapter 15](15-Chapter-15-Using-Conditionals.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 17 →](17-Chapter-17-Unusual-Control-Structures.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Loops (`while`, `for`, `foreach`) are the primary engine for iterative computing and the most frequent source of execution bugs: **infinite loops, off-by-one boundary errors, uninitialized accumulators, and index corruption**.

Steve McConnell's central loop dogma is: **Treat loop initialization, termination, and body operations as three separate, disciplined concerns**. Keep loop bodies short, keep index variables local, and ensure loop termination is mathematically guaranteed.

---

## 2. Theoretical Foundations & Key Concepts

### 1. Choosing the Right Loop Structure
- **Counted Loops (`for`)**: Use when the number of iterations is known in advance.
- **Conditional Loops (`while`)**: Use when termination depends on a runtime condition or event.
- **Collection / Iterator Loops (`foreach`, `for..of`)**: Preferred by default when iterating over collections to eliminate index management and off-by-one risks.

### 2. Controlling Loop Boundaries
- **Off-by-One Protection**: Explicitly evaluate whether the boundary should be `<` or `<=`. Test with iteration limits of 0, 1, and $N$.
- **Never Modify Index Variables Inside a Counted Loop**: Do not tamper with `i` inside a `for (let i = 0; i < n; i++)` body. If you need dynamic step jumping, use a `while` loop.
- **Safety Counters**: For `while` loops dependent on network I/O or external resources, include a hard maximum iteration guard to prevent infinite loops.

### 3. Exiting Loops
- **Single Point of Control**: Prefer loop conditions in the header rather than scattering multiple exits throughout the body.
- **Break vs. Flags**: Use `break` to exit early once a match is found; avoid introducing artificial boolean flags (`bool done = false`) solely to control loop termination.
- **Never use `continue` carelessly**: `continue` can bypass essential loop advancement or cleanup code at the bottom of the loop body.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 16.1 — Favor Iterators over Index Loops**: Use collection iterators (`for item in items`, `items.map()`, `for (const x of items)`) instead of manual indexing loops whenever possible.
2. **RULE 16.2 — Immutability of Loop Indices**: Never modify the index variable inside the body of a `for` loop.
3. **RULE 16.3 — Mandatory Safety Bound on While Loops**: Any `while` loop awaiting an external event or convergence condition must possess a finite safety limit ($N_{\max}$) to prevent infinite execution.
4. **RULE 16.4 — Re-initialize Accumulators Immediately Before Loop**: Initialize loop variables immediately prior to the loop statement, not at the top of the function.
5. **RULE 16.5 — Keep Loops Short**: Loop bodies should fit on a single screen (typically $\le 20-30$ lines). If a loop body is long, extract the interior into a worker routine.

---

## 4. Operational Checklist Derived from McConnell: Controlling Loops

- [ ] Is the correct loop type selected (iterator > counted > conditional)?
- [ ] Are all loop variables initialized immediately before the loop?
- [ ] Is the loop termination condition explicitly guaranteed to occur?
- [ ] Are boundary conditions ($0, 1, N-1, N$) tested?
- [ ] Is the loop index variable unmodified inside the loop body?
- [ ] Is the loop body short and focused?
- [ ] Are safety bounds installed to prevent infinite while loops?

---

## 5. Heuristics & Metrics

- **Loop Length**: Long loop bodies are candidates for extraction when cohesion or readability suffers; there is no universal line limit.
- **Nesting**: Nested loops deserve review for clarity and complexity, but natural multidimensional algorithms may legitimately require them.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Infinite Loop Risk and Index Tampering
```python
# BAD: Modifying index inside for loop; while loop with no safety guard
def process_batches(records):
    for i in range(len(records)):
        if records[i].is_skipped:
            i += 2 # DANGEROUS: Tampering with index variable!

    while not check_remote_status(): # RISKY: What if remote never succeeds? Infinite freeze!
        sleep(1)
```

### Pattern: Iterators, Explicit Step Logic, and Safety Bound
```python
# GOOD: Clean iterator, safety iteration cap on conditional loop
import time

MAX_RETRIES = 10
RETRY_DELAY_SECONDS = 0.5

def await_remote_sync(checker_fn, max_retries: int = MAX_RETRIES) -> bool:
    """Poll for remote status with an absolute safety bound."""
    attempts = 0
    while attempts < max_retries:
        if checker_fn():
            return True
        attempts += 1
        if attempts < max_retries:
            time.sleep(RETRY_DELAY_SECONDS)

    raise TimeoutError(f"Remote sync failed to complete after {max_retries} attempts")
```

---

## 7. Inspection & Verification Protocol

1. Audit `while` loops whose termination depends on external events or uncertain convergence: is there an appropriate timeout, cancellation, or bound?
2. Verify boundary conditions: verify whether empty collections execute 0 iterations safely.
3. Ensure loop counters are scoped strictly to the loop header.
