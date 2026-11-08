# Chapter 22: Developer Testing

[← Previous: Chapter 21](21-Chapter-21-Collaborative-Construction.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 23 →](23-Chapter-23-Debugging.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Developer testing is the primary feedback loop of software construction. Testing is not a downstream activity relegated to a separate QA team; it is an intrinsic part of authoring code.

Steve McConnell establishes that **Testing is the search for defects**. The goal of testing is not to prove that the code works under sunny conditions, but to make the code break under adverse, boundary, and pathological conditions. A suite that never finds defects may indicate excellent code, weak tests, or both; evaluate what the tests are capable of detecting.

---

## 2. Theoretical Foundations & Key Concepts

### Developer Testing vs. Independent QA Testing
- **Developer Testing**: Focuses on white-box and component testing (unit tests, integration tests) to ensure internal correctness, edge-case coverage, and structural integrity.
- **Independent Testing**: Focuses on black-box, system, security, and acceptance testing from an end-user perspective.

### Systematic Test Case Design Techniques

1. **Structured Basis Testing (McCabe Basis Paths)**:
   - Calculate cyclomatic complexity $v(G)$ of the routine.
   - Design at least $v(G)$ deliberate cases for a basis set. Arbitrary cases of that count do not guarantee basis-path, branch, or data coverage.
2. **Boundary Value Analysis**:
   - The vast majority of coding defects occur at boundaries.
   - For an integer range $[1 \dots 100]$, test:
     - Just below boundary: $0$
     - Exactly on boundaries: $1, 100$
     - Interior normal value: $50$
     - Just above boundary: $101$
3. **Equivalence Partitioning**:
   - Divide input domain into partitions where behavior is expected to be identical.
   - Test one representative value from each valid partition and one from each invalid partition.
4. **Bad Data Injection**:
   - Inject uninitialized inputs, null/None, empty collections, wrong types, inverted ranges, and extremely large payloads to verify defensive behavior.

### Test-First Development (TDD)
Writing tests before code can clarify interfaces and expose unworkable designs early, but it does not guarantee complete testability or correctness.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 22.1 — Basis Path Coverage**: Where white-box testing is appropriate, use $v(G)$ as an aid for deliberate branch and basis coverage, then supplement it with data and requirement tests.
2. **RULE 22.2 — Risk-Based Boundary Testing**: Test meaningful minimum, maximum, empty, and off-by-one boundaries for affected loops, ranges, and buffers, following project testing conventions.
3. **RULE 22.3 — Test Bad Data and Exceptions**: Do not test only the "happy path." Explicitly test invalid inputs, null pointers, empty strings, and assert that expected exceptions are thrown.
4. **RULE 22.4 — Deterministic Tests**: Make tests independent of execution order and avoid real timing waits where fake clocks or synchronization primitives are available. Integration tests may share managed resources by design.
5. **RULE 22.5 — Mock Boundaries, Not the Behavior Under Test**: Use mocks at external or intentionally isolated boundaries; do not replace the domain behavior that the test is meant to verify.

---

## 4. Operational Checklist Derived from McConnell: Developer Testing

- [ ] Does test coverage include all basis paths calculated from cyclomatic complexity?
- [ ] Are boundary values ($0, 1, N-1, N$) explicitly tested?
- [ ] Are invalid and unexpected inputs tested (bad data injection)?
- [ ] Are tests automated, deterministic, and runnable in a single command?
- [ ] Do tests verify error messages and exception types?
- [ ] Are test suites maintained and run before every commit?

---

## 5. Heuristics & Metrics

- **Code Coverage**: Use statement and branch coverage diagnostically; no universal percentage proves correctness or adequate boundary coverage.
- **Test-to-Code Ratio**: Test volume depends on behavior and risk, not a universal lines-of-test to lines-of-production ratio.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Superficial "Happy Path" Testing
```python
# BAD: Only tests happy path with a single input; no boundary or error checking
def test_discount():
    assert calculate_discount(100) == 10 # Passes, but masks off-by-one and negative input bugs!
```

### Pattern: Exhaustive Equivalence, Boundary, and Error Testing
```python
# GOOD: Comprehensive boundary, equivalence, and exception tests using pytest
import pytest

DISCOUNT_THRESHOLD_CENTS = 10_000
DISCOUNT_PERCENT = 10

def calculate_discount(amount_cents: int) -> int:
    if amount_cents < 0:
        raise ValueError(f"Negative amount: {amount_cents}")
    if amount_cents >= DISCOUNT_THRESHOLD_CENTS:
        return amount_cents * DISCOUNT_PERCENT // 100
    return 0

# 1. Negative error case
def test_calculate_discount_rejects_negative():
    with pytest.raises(ValueError, match="Negative amount"):
        calculate_discount(-1)

# 2. Boundary: zero
def test_calculate_discount_zero():
    assert calculate_discount(0) == 0

# 3. Boundary: just below discount threshold ($99.99)
def test_calculate_discount_below_threshold():
    assert calculate_discount(9999) == 0

# 4. Boundary: exactly on discount threshold ($100.00)
def test_calculate_discount_at_threshold():
    assert calculate_discount(10000) == 1000

# 5. Normal above threshold
def test_calculate_discount_above_threshold():
    assert calculate_discount(25000) == 2500
```

---

## 7. Inspection & Verification Protocol

1. Run the relevant test suite: confirm changed tests pass and explain any unrelated or pre-existing failures.
2. Verify that tests fail when intentional boundary errors are introduced into the code.
