# Chapter 24: Refactoring

[← Previous: Chapter 23](23-Chapter-23-Debugging.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 25 →](25-Chapter-25-Code-Tuning-Strategies.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Software systems evolve through accretion; without continuous structural renewal, entropy degrades the codebase into an unmaintainable tangle. **Refactoring** is the disciplined technique of altering the internal structure of code without changing its observable external behavior.

Steve McConnell and Martin Fowler establish the cardinal rule of refactoring: **Take small, incremental steps and run automated tests after every single transformation**. Never combine refactoring with adding new features.

---

## 2. Theoretical Foundations & Key Concepts

### Code Smells (When to Refactor)
1. **Duplicate Code**: The same code expression appears in multiple places. Extract routine.
2. **Long Routine**: Exceeds cognitive limits or handles multiple tasks. Decompose.
3. **Large Class / God Class**: Low cohesion, too many instance variables. Split class.
4. **Long Parameter List**: More than 5 arguments traveling together. Introduce Parameter Object.
5. **Divergent Change**: One class is commonly modified in different ways for different reasons. Violates SRP.
6. **Shotgun Surgery**: Every time you make a change, you must make small edits to 10 different classes. Consolidate responsibility.
7. **Feature Envy**: A method accesses the data of another object more than its own. Move method.
8. **Primitive Obsession**: Using raw strings and numbers instead of small domain classes.

### The Safe Refactoring Protocol
1. **Use Available Safety Nets**: Run existing regression tests and add proportionate characterization checks within project conventions where useful. Disclose meaningful coverage gaps.
2. **Small Incremental Steps**: Perform one microscopic refactoring at a time (e.g. rename variable, extract routine).
3. **Test After Every Step**: Run unit tests immediately after each transformation. If tests fail, revert immediately; do not debug a multi-step refactoring mess.
4. **Strict Separation of Concerns**: Keep refactoring commits completely separate from feature additions or bug fixes.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 24.1 — Safety Net and Risk**: Use passing existing tests where available. Their absence requires proportionate verification and disclosure, not an automatic block on every requested refactoring.
2. **RULE 24.2 — Atomic Transformations**: Apply refactorings incrementally. Run tests after every single extraction or rename.
3. **RULE 24.3 — Zero Behavioral Drift**: Verify that input/output behavior, return codes, and exception contracts remain identical before and after refactoring.
4. **RULE 24.4 — Separate Refactoring from Feature Work**: Do not refactor surrounding code opportunistically while implementing a feature. Keep necessary refactoring explicit and separately verifiable; commit separation requires user authorization and project convention.

---

## 4. Operational Checklist Derived from McConnell: Refactoring

- [ ] Does a comprehensive suite of passing tests exist before starting?
- [ ] Are refactorings performed in small, verifiable steps?
- [ ] Are tests executed after every individual change?
- [ ] Are code smells (duplication, long routines, god classes) explicitly targeted?
- [ ] Is external behavior guaranteed unchanged?
- [ ] Are refactoring changes segregated from functional enhancements?

---

## 5. Heuristics & Metrics

- **Step Size**: Prefer independently verifiable transformations; coherent migrations may legitimately span many files.
- **Duplication Threshold**: Extract duplication when a shared abstraction improves correctness or maintenance, not merely because several lines match.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Monolithic Code Smell (Duplicate logic and Feature Envy)
```python
# BAD: Duplicated calculation, feature envy, hardcoded logic
def print_invoice(order):
    # Calculation 1
    subtotal = 0
    for item in order.items:
        subtotal += item.price * item.quantity
    tax = subtotal * 0.08
    print(f"Total: {subtotal + tax}")

def export_invoice_json(order):
    # Duplicate of calculation 1!
    subtotal = 0
    for item in order.items:
        subtotal += item.price * item.quantity
    tax = subtotal * 0.08
    return {"total": subtotal + tax}
```

### Pattern: Extracted Domain Calculation (Clean, DRY Refactoring)
```python
# GOOD: Calculation extracted to domain object, pure and reusable
from decimal import Decimal
from typing import Sequence

class OrderItem:
    price: Decimal
    quantity: int

class Order:
    items: Sequence[OrderItem]

    @property
    def subtotal(self) -> Decimal:
        return sum((item.price * item.quantity for item in self.items), Decimal("0.00"))

    @property
    def sales_tax(self) -> Decimal:
        return (self.subtotal * Decimal("0.08")).quantize(Decimal("0.01"))

    @property
    def grand_total(self) -> Decimal:
        return self.subtotal + self.sales_tax
```

---

## 7. Inspection & Verification Protocol

1. Run the regression test suite before starting: confirm all green.
2. Apply refactoring step.
3. Run relevant tests again: confirm they pass and compare observable behavior, noting any pre-existing failures.
