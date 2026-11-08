# Chapter 15: Using Conditionals

[← Previous: Chapter 14](14-Chapter-14-Organizing-Straight-Line-Code.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 16 →](16-Chapter-16-Controlling-Loops.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Conditionals (`if`, `if-else`, `switch`, `case`) control the execution branches of software. Deeply nested, tangled conditionals are the primary cause of logic errors and cyclomatic complexity explosion.

A useful engineering guideline for conditionals is: **make the nominal path clear, use guard clauses where they help, and review deep nesting for readability**. Write conditions so that the normal, happy path reads straight down, and handle edge cases and errors immediately as guards.

---

## 2. Theoretical Foundations & Key Concepts

### 1. `if-then-else` Statements
- **Write the nominal path first**: Put the normal, expected case inside the `if` clause, and error/exceptional cases in the `else` clause (or use guard clauses).
- **Make sure you branch on equality correctly**: Compare variables in the natural reading order (`if (MIN_LIMIT <= value && value <= MAX_LIMIT)`).
- **Avoid empty `then` branches**: Never write `if (condition) ; else doWork();`. Invert the condition.

### 2. Guard Clauses vs. Deep Nesting
- **Arrow Anti-pattern (Pyramid of Doom)**: Deeply indented nested `if` statements ($> 3$ levels) force the reader to hold multiple nested predicates in working memory.
- **Guard Clauses**: Check error and boundary conditions at the beginning of the routine and return immediately (`return`, `continue`, `break`). The remaining code executes flat and un-nested.

### 3. `case` / `switch` Statements
- **Order cases logically**: Order cases alphabetically, by frequency of occurrence, or by numerical sequence.
- **Keep actions simple**: Do not write 30 lines of code inside a `case` block; call a dedicated routine.
- **Always provide a `default` case**: Use the default case to trap unexpected values or assert invariants (`throw new UnhandledCaseException()`).
- **Prevent accidental fallthrough**: If your language supports fallthrough (C, C++, Java), explicitly comment or use `[[fallthrough]]` annotations; better yet, avoid fallthrough entirely.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 15.1 — Nesting Review**: Around three or more nesting levels often justify simplification through guard clauses, boolean decomposition, or tables, but depth alone is not a defect.
2. **RULE 15.2 — Early Exit via Guard Clauses**: Handle invalid preconditions and other early-out cases near the function entry when that clarifies the nominal path.
3. **RULE 15.3 — Handle Remaining Cases Deliberately**: Make `else` and `default` behavior explicit when unhandled values are possible; do not add empty branches solely to satisfy a template.
4. **RULE 15.4 — Express Conditions Positively**: Avoid negative conditional expressions like `if (!isUnregistered)`. Invert to `if (isRegistered)`.

---

## 4. Operational Checklist Derived from McConnell: Using Conditionals

- [ ] Is the nominal execution path written first?
- [ ] Are error conditions handled upfront with guard clauses?
- [ ] Is conditional nesting kept to 3 levels or fewer?
- [ ] Are conditions phrased positively?
- [ ] Do all `switch`/`case` statements include an explicit `default` clause?
- [ ] Are case actions kept short by delegating to sub-routines?
- [ ] Are accidental fallthrough bugs prevented?

---

## 5. Heuristics & Metrics

- **Nesting Depth ($N$)**:
  - Lower nesting is often easier to read.
  - Around $N \ge 3$: inspect whether guard clauses or extraction would improve clarity.
  - No depth is an automatic rejection criterion without project policy.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: The Pyramid of Doom (Deep Nesting)
```javascript
// BAD: Deep nesting, nominal path buried at level 4, hard to read
function processOrder(order) {
  if (order !== null) {
    if (order.isValid) {
      if (order.hasPayment) {
        if (order.inStock) {
          shipOrder(order);
        } else {
          showError("Out of stock");
        }
      } else {
        showError("No payment");
      }
    } else {
      showError("Invalid order");
    }
  }
}
```

### Pattern: Clean Guard Clauses (Flat, Linear Control Flow)
```typescript
// GOOD: Guard clauses handle errors early, leaving nominal path flat
export function processOrder(order: Order | null): void {
  if (!order || !order.isValid) {
    showError("Invalid order");
    return;
  }
  if (!order.hasPayment) {
    showError("No payment");
    return;
  }
  if (!order.inStock) {
    showError("Out of stock");
    return;
  }

  // Nominal path executes flat and cleanly
  shipOrder(order);
}
```

---

## 7. Inspection & Verification Protocol

1. Count the indentation levels of all `if` blocks: verify none exceed 3.
2. Confirm every `switch` statement has an explicit `default` block.
3. Verify that guard clauses return early to keep main logic un-nested.
