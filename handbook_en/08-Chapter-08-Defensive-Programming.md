# Chapter 8: Defensive Programming

[← Previous: Chapter 7](07-Chapter-07-High-Quality-Routines.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 9 →](09-Chapter-09-The-Pseudocode-Programming-Process.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Defensive programming is built on a single axiom: **Programs must protect themselves against invalid data and unexpected events**. The naive philosophy of *"Garbage in, Garbage out"* has no place in professional engineering; the professional standard is **"Garbage in, Nothing out"** or **"Garbage in, Error out"**.

However, defensive programming must be applied systematically. Excessive, undisciplined defensive checks cause bloat and obscure business logic. The solution is the **Barricade Architecture**: establish strict security/sanitization perimeters at external interfaces, and enforce invariants internally using **Assertions**.

---

## 2. Theoretical Foundations & Key Concepts

### Assertions vs. Error Handling
- **Assertions**:
  - Used for conditions that should **NEVER** happen if the code is correct (internal invariant checks, pre-conditions, post-conditions).
  - Assertions check for bugs in the *programmer's code*, not user errors.
  - Assertions should not contain executable side effects (they might be compiled out in release builds).
- **Error-Handling Code**:
  - Used for conditions that are *anticipated to happen* occasionally in production (bad user input, missing files, network dropouts, disk full).
  - Must remain in release builds and gracefully manage the failure.

### Error-Handling Techniques
1. **Return a neutral value**: (e.g. empty string, 0, empty list) when safe.
2. **Substitute next valid data**: (e.g. in video streams or sensors, use the previous frame).
3. **Return previous value**: (e.g. thermometer read fails, return last recorded reading).
4. **Substitute closest legal value**: (e.g. clamp negative volume to 0).
5. **Log error and continue**: appropriate when fault isolation is paramount.
6. **Return an explicit error code / Result type**: (`Result<T, E>`) forcing caller to handle failure.
7. **Throw an exception**: when local handling is impossible and caller must be alerted.
8. **Fail Fast / Shut Down**: in safety-critical or security domains, immediate shutdown prevents catastrophic damage or data corruption.

### Barricades (Sanitization Perimeters)
Designate clear boundaries between the untrusted outside world and the trusted inside world:
- **Dirty Zone (External)**: HTTP requests, file inputs, user form data, external API responses. Validate, sanitize, and convert to safe internal types immediately.
- **Barricade**: The converter/validator that converts raw inputs into strictly typed domain models.
- **Clean Zone (Internal)**: Inside the core domain, data is guaranteed valid. Use assertions to verify internal logic rather than endlessly re-validating the same data.

### Offensive Programming (Fail Fast in Development)
During development, make defects as visible, dramatic, and painful as possible:
- Crash immediately on unexpected nulls.
- Fill newly allocated or freed memory with garbage bytes (`0xDEADBEEF`).
- Ensure assertions fail loudly so bugs cannot lurk undetected into production.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 8.1 — Separate Assertions from Exceptions**:
   - Never use assertions to handle expected runtime errors (e.g. bad user passwords, missing remote files).
   - Never use exceptions where a normal control flow or neutral value is appropriate.
2. **RULE 8.2 — Zero Side Effects in Assertions**: An assertion must only inspect state. It must never call a function that alters state (e.g. `assert(list.pop() == expected)` is strictly forbidden).
3. **RULE 8.3 — Build Strict Barricades**: Sanitize all external inputs at system entry points. Convert untrusted strings/numbers into validated value objects immediately.
4. **RULE 8.4 — Never Swallow Errors Silently**: Do not write empty handlers that hide failures. Handle the error, or add useful context and re-raise; avoid duplicate logging and sensitive payloads in diagnostics.
5. **RULE 8.5 — Handle Specific Exceptions**: Catch only the exact exceptions anticipated (`FileNotFoundError`, `KeyError`, `ValueError`). Never catch top-level `Exception` unless at the global supervisor boundary.

---

## 4. Operational Checklist Derived from McConnell: Defensive Programming

- [ ] Are assertions used to check conditions that should never happen?
- [ ] Are assertions free of executable side effects?
- [ ] Is production error handling clearly distinguished from internal assertions?
- [ ] Are input parameters sanitized at the boundary (Barricade)?
- [ ] Are exceptions used exclusively for truly exceptional conditions?
- [ ] Do catch blocks handle specific exception types without swallowing errors?
- [ ] Does the program fail fast in development mode?
- [ ] Is the error-handling strategy consistent across the entire codebase?

---

## 5. Heuristics & Metrics

- **Assertion Density**: Use assertions for meaningful programmer invariants; no assertions-per-line quota improves reliability by itself.
- **Barricade Depth**: Validate at real ingress boundaries. Internal services should rely on explicit validated contracts rather than repeating identical checks.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Assertion with Side Effects and Swallowed Exception
```python
# BAD: Side effect inside assert; swallowing errors silently
def process_queue(queue):
    # DANGEROUS: If assertions are disabled (-O), pop() is never called!
    assert queue.pop(0) is not None

    try:
        perform_risky_io()
    except Exception:
        pass # Catastrophic bug hiding: silent swallow!
```

### Pattern: Clean Barricade and Proper Assertion Usage
```python
# GOOD: Boundary sanitization, explicit exception handling, clean internal invariant check
from typing import Sequence

class BarricadeValidationError(ValueError):
    """Raised at the boundary when untrusted input is malformed."""
    pass

# Barricade layer
def sanitize_port(raw_port: str) -> int:
    try:
        port = int(raw_port)
    except (ValueError, TypeError) as exc:
        raise BarricadeValidationError(f"Port must be integer, got {raw_port!r}") from exc
    if not (1 <= port <= 65535):
        raise BarricadeValidationError(f"Port out of range [1-65535]: {port}")
    return port

# Internal clean domain layer
def allocate_connection_slot(pool: list[int], port: int) -> None:
    # Internal assertion: port was already guaranteed valid by barricade
    assert 1 <= port <= 65535, f"Invariant violated: invalid port {port} breached barricade"
    pool.append(port)
```

---

## 7. Inspection & Verification Protocol

1. Scan all `assert` statements: verify none contain mutating function calls or assignments.
2. Verify all catch/except blocks catch specific error types and log full diagnostics.
3. Check that external inputs (APIs, CLI flags, files) are validated before passing into internal logic.
