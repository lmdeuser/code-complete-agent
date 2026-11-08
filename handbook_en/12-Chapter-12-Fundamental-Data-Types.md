# Chapter 12: Fundamental Data Types

[← Previous: Chapter 11](11-Chapter-11-The-Power-of-Variable-Names.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 13 →](13-Chapter-13-Unusual-Data-Types.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Fundamental data types—integers, floating-point numbers, characters, strings, booleans, and enumerations—are the atomic building blocks of software. Operating on raw primitives without recognizing their physical machine limitations causes catastrophic errors: **integer overflow, floating-point roundoff drift, off-by-one string indexing, and magic number pollution**.

AI Agents must treat fundamental data types with defensive rigor: avoid raw magic literals, use domain-appropriate numerical precision, and eliminate primitive obsession through strongly typed domain constructs and enumerations.

---

## 2. Theoretical Foundations & Key Concepts

### 1. Integers
- **Overflow & Underflow**: In fixed-width integer arithmetic (32-bit, 64-bit), exceeding maximum values results in wrap-around.
- **Integer Division**: Rounding and truncation are language-specific (for example, C++ truncates toward zero while Python `//` floors). Check semantics, overflow, and required rounding before reordering integer formulas.

### 2. Floating-Point Numbers
- **Binary Inexactness**: Binary floating-point numbers (IEEE 754 `float`, `double`) cannot exactly represent most decimal fractions (e.g. $0.1 + 0.2 \ne 0.3$).
- **Approximate Equality**: Use relative and absolute tolerances for calculated values when representation error matters. Exact comparison remains valid for sentinels, zero checks, and intentionally exact values.
- **Financial Rule**: **NEVER use floating-point types for currency or financial math**. Use integer cents, `Decimal`, or fixed-point representations.

### 3. Characters and Strings
- **Unicode & UTF-8**: Determine encodings explicitly at byte/string boundaries, using UTF-8 when that is the project or protocol standard. Distinguish bytes, code points, and grapheme clusters.
- **Off-by-One Indexing**: String slices and index bounds are the most common source of boundary defects.
- **String Immutability & Allocation**: In languages with immutable strings, string concatenation in loops produces $O(n^2)$ memory churning. Use string builders or join lists.

### 4. Booleans
- Use booleans to simplify complex expressions.
- Apply **De Morgan's Laws** to simplify negated compound expressions:
  - `!(A && B)` is equivalent to `!A || !B`
  - `!(A || B)` is equivalent to `!A && !B`

### 5. Enumerated Types (Enums)
- Enums make code readable, self-documenting, and type-safe.
- Use enums instead of boolean parameters when a function represents more than two states, or when future states may be added.

### 6. Named Constants
- Eliminate all **Magic Numbers** (naked numeric literals like `3.14159`, `86400`, `1024`).
- Define centralized, descriptive constants with a single point of truth.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 12.1 — Appropriate Float Comparison**: Use language-provided approximate comparison for calculated values where rounding matters, with domain-appropriate tolerances. Keep intentional exact comparisons.
2. **RULE 12.2 — Zero Floats for Currency**: In financial calculations, use fixed-point arithmetic, arbitrary-precision `Decimal`, or integer cents.
3. **RULE 12.3 — Clarify Magic Values**: Name numeric and string literals when their meaning, ownership, or repetition is unclear; keep self-evident literals when a constant would add noise.
4. **RULE 12.4 — Use Enums over Boolean Arguments**: If a function takes a boolean flag that alters its behavior, replace the boolean with an explicit enum (`ExecutionMode::Synchronous` vs `ExecutionMode::Asynchronous`).
5. **RULE 12.5 — Explicit String Encodings**: When reading or writing bytes to strings, explicitly specify the encoding (`encoding='utf-8'`). Never rely on system default locales.

---

## 4. Operational Checklist Derived from McConnell: Fundamental Data Types

- [ ] Are integer division operations protected against unexpected truncation and zero division?
- [ ] Are all floating-point comparisons performed with an explicit epsilon tolerance?
- [ ] Is financial arithmetic strictly isolated from binary floating-point types?
- [ ] Are strings handled with explicit awareness of UTF-8 encoding and index bounds?
- [ ] Are magic numbers replaced with descriptive named constants?
- [ ] Are enumerations used in place of magic string/integer flags?
- [ ] Are complex boolean expressions simplified using De Morgan's laws?

---

## 5. Heuristics & Metrics

- **Magic Number Density**: Review unexplained literals by meaning and duplication, not by a zero-literal quota.
- **Float Comparison Precision**: Choose relative and absolute tolerances from the calculation, scale, and required correctness; fixed values such as $10^{-6}$ are not universal domain defaults.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Float Comparison and Currency Float Disaster
```python
# BAD: Direct float equality and using floats for monetary calculations
def process_refund(balance, refund_amount):
    balance = balance - refund_amount # Precision drift over time!
    if balance == 0.0: # RISKY: Exact equality may be inappropriate after approximate calculations
        print("Account settled")
    return balance
```

### Pattern: Precise Currency, Named Constants, and Safe Float Tolerance
```python
# GOOD: Decimal for currency, named constants, and epsilon tolerance for floats
from decimal import Decimal
import math

ANNUAL_INTEREST_RATE = Decimal("0.045")
FLOAT_EPSILON = 1e-9

def compare_coordinates(coord_a: float, coord_b: float) -> bool:
    """Compare spatial coordinates safely using tolerance threshold."""
    return math.isclose(coord_a, coord_b, abs_tol=FLOAT_EPSILON)

def apply_accrued_interest(account_balance: Decimal) -> Decimal:
    """Calculate interest using decimal arithmetic with explicit precision and rounding where required."""
    if account_balance < Decimal("0.00"):
        raise ValueError(f"Cannot accrue interest on negative balance: {account_balance}")
    interest = account_balance * ANNUAL_INTEREST_RATE
    return account_balance + interest
```

---

## 7. Inspection & Verification Protocol

1. Grep for `==` applied to floating-point variables: replace with approximate comparison.
2. Check all monetary calculations: ensure they use integer cents or `Decimal`.
3. Search for raw numeric literals: extract into module-level named constants.
