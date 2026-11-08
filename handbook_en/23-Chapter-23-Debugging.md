# Chapter 23: Debugging

[← Previous: Chapter 22](22-Chapter-22-Developer-Testing.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 24 →](24-Chapter-24-Refactoring.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Debugging is the systematic process of finding and fixing the root cause of an identified defect. It is not an opportunity for superstitious guesswork, random code tweaking, or blaming compilers and tools.

Steve McConnell insists on the **Scientific Debugging Method**: *Formulate a hypothesis, design an experiment to test the hypothesis, observe the outcome, and locate the fundamental root cause before touching a single line of code*. If you fix a symptom without understanding the root cause, you inevitably inject new defects.

---

## 2. Theoretical Foundations & Key Concepts

### The Scientific Debugging Protocol
1. **Stabilize the Defect**:
   - Reduce the failure to the simplest, reliably reproducible test case.
   - Isolate the exact input payload and system configuration that triggers the bug.
2. **Locate the Defect (Hypothesis-Driven)**:
   - Formulate a clear hypothesis: *"The loop boundary allows index $i=N$ to execute, causing out-of-bounds access."*
   - Design an experiment to verify or refute: inspect variables via debugger, logging, or isolated test.
   - Narrow the search window using binary search (bisection) through code or git history (`git bisect`).
3. **Fix the Defect**:
   - Understand the entire surrounding context before editing.
   - Verify that the fix does not break related invariants.
   - Fix the *cause*, not the *symptom* (e.g. don't mask a null pointer with `if (x != null)` if `x` should never have been null in the first place).
4. **Verify the Fix**:
   - Run the reproducible test case: confirm it passes.
   - Run the entire test suite: confirm zero regressions.
5. **Look for Similar Defects**:
   - Ask: *"Where else in the codebase was this same mistake or faulty assumption made?"*

### Psychological Traps in Debugging
- **Confirmation Bias**: Looking only for evidence that confirms your initial hunch while ignoring contrary logs.
- **Superstitious Tweaking**: Randomly changing `+1` to `-1` or adding arbitrary sleeps until the bug disappears. This is an engineering crime.
- **Blaming the Toolchain**: Assuming the compiler, operating system, or library has a bug. Usually the defect is in application code, but toolchain bugs are possible and should be evaluated from evidence rather than superstition.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 23.1 — Reproduce Before Modifying**: Never apply code changes until you can reliably reproduce the error or point to the verifiable logical defect.
2. **RULE 23.2 — Root Cause over Symptom Masking**: Never wrap buggy logic in a blind `try/catch` or band-aid `if (var == null) return` without proving why the state was invalid.
3. **RULE 23.3 — The Rule of Two Failed Attempts**: If an attempted fix fails twice, stop tweaking. Your mental model of the problem is wrong. Step back, re-read the code from scratch, and re-diagnose root cause.
4. **RULE 23.4 — Check for Identical Defects**: Whenever you find and fix a bug, grep for similar patterns across the repository.
5. **RULE 23.5 — Add a Regression Test Where Feasible**: Within existing project testing conventions, reproduce the defect before the fix and verify the corrected behavior afterward; disclose when automation is not practical.

---

## 4. Operational Checklist Derived from McConnell: Debugging

- [ ] Has the defect been reduced to a minimal reproducible test case?
- [ ] Was the defect located using hypothesis-driven experiments rather than random guessing?
- [ ] Is the fundamental root cause understood rather than masked?
- [ ] Does the fix preserve all surrounding invariants without introducing regressions?
- [ ] Has an automated regression test been added to the test suite?
- [ ] Were similar defects searched for and remediated across the codebase?

---

## 5. Heuristics & Metrics

- **Regression Test Coverage**: Cover fixed defects with automated checks where feasible and proportionate, without treating a universal percentage as proof of correctness.
- **Time to Stabilize**: Prioritize locating and understanding the defect over random edits; the appropriate effort split varies by bug.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Masking the Symptom (Band-Aid Fix)
```python
# BAD: Masking root cause. Why was user None? Why was balance missing?
def render_profile(user):
    if user is None: # Hides the fact that database query returned corrupt data!
        return ""
    if not hasattr(user, 'balance'): # Band-aid masking broken schema initialization!
        return f"{user.name}: $0"
    return f"{user.name}: ${user.balance}"
```

### Pattern: Root Cause Fix with Regression Guard
```python
# GOOD: Fix root cause at source (data loader) and add strict invariant assertion
from decimal import Decimal
from typing import NamedTuple

class UserProfile(NamedTuple):
    id: str
    name: str
    balance_cents: int

def load_user_profile(user_data: dict) -> UserProfile:
    """Fixed root cause: ensure parser enforces complete valid profile."""
    if "id" not in user_data or "name" not in user_data or "balance_cents" not in user_data:
        raise KeyError(f"Malformed user profile payload: {user_data.keys()}")

    return UserProfile(
        id=str(user_data["id"]),
        name=str(user_data["name"]),
        balance_cents=int(user_data["balance_cents"])
    )

def render_profile(profile: UserProfile) -> str:
    # Guaranteed clean domain object; no masking required
    balance = Decimal(profile.balance_cents) / Decimal(100)
    return f"{profile.name}: ${balance:.2f}"
```

---

## 7. Inspection & Verification Protocol

1. Verify that the regression test fails on the original code and succeeds on the modified code.
2. Confirm that no error was silently swallowed or masked.
