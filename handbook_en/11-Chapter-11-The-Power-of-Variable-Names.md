# Chapter 11: The Power of Variable Names

[← Previous: Chapter 10](10-Chapter-10-General-Issues-in-Using-Variables.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 12 →](12-Chapter-12-Fundamental-Data-Types.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Naming is the cornerstone of self-documenting code. Code is read dozens of times more often than it is written. A vague, cryptic, or inaccurate variable name inflicts cognitive friction upon every future reader and agent. Steve McConnell notes that good names do not describe *how* a variable is implemented in computer memory, but **WHAT problem-domain entity it represents**.

Variable names must be completely clear, unambiguous, pronounceable, and accurately matched to their scope.

---

## 2. Theoretical Foundations & Key Concepts

### Characteristics of Quality Names
1. **Problem-Domain Centric**: Name variables after the real-world concept (`salesTaxRate`), not the computer representation (`floatVal`, `arrData`).
2. **Name Length**: Research cited by McConnell discusses useful average ranges around **8–20 characters**, but this is not a rule for every identifier. Clarity and scope matter more than character count.
3. **Scope-Dependent Length**:
   - Short names (`i`, `j`) are acceptable *only* for local loop indices spanning 2–3 lines.
   - Variables with wider scope (class members, exported constants) require longer, highly descriptive names (`customerBillingAddress`).

### Naming Specific Kinds of Variables
- **Loop Indices**:
  - Acceptable: `i`, `j`, `k` for small loops under 5 lines.
  - Required for nested loops: `rowIndex`, `colIndex`, `studentIdx`.
- **Status Flags**:
  - Name should describe the state itself: `isReady`, `hasError`, `processingComplete`.
  - Avoid negative names: `isNotFound` or `disabled` lead to horrific double-negatives (`if (!isNotFound)`).
- **Temporary Variables**:
  - The name `temp` is an admission of failure. Replace with descriptive terms: `intermediateSum`, `clonedHeader`.
- **Booleans**:
  - Must imply `true` or `false`: `isEmpty`, `canWrite`, `hasPrivileges`, `isValid`.
  - Avoid ambiguous nouns: `status`, `check`, `flag`.

### Conventional Qualifying Suffixes
When qualifying a variable, put the modifier at the *end* of the name to maintain alphabetical grouping and conceptual clarity:
- Preferred: `revenueTotal`, `revenueAverage`, `revenueMaximum`.
- Less optimal: `totalRevenue`, `avgRevenue`, `maxRevenue`.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 11.1 — Describe the "What", Not the "How"**: Do not embed storage types in names (avoid Hungarian notation like `iCount`, `strName`, `arrList`). Name after business meaning: `employeeCount`, `customerName`, `pendingOrders`.
2. **RULE 11.2 — Prefer Positive Boolean Phrasing**: Prefer clear positive identifiers, especially to avoid double negatives. A negative name can remain appropriate when it models the domain more directly.
3. **RULE 11.3 — Avoid Unclear Generic Names**: Avoid `data`, `info`, `temp`, `val`, and single-character names when they hide meaning; conventional short names are valid in small, clear scopes.
4. **RULE 11.4 — Qualifiers at the Suffix**: Standardize aggregate qualifiers at the end of the identifier (`Count`, `Index`, `Total`, `Average`).
5. **RULE 11.5 — Pronounceable and Searchable**: Ensure all identifiers can be searched via grep without colliding with common substrings.

---

## 4. Operational Checklist Derived from McConnell: Variable Names

- [ ] Does the name describe the real-world problem rather than the programming construct?
- [ ] Is the name between 8 and 20 characters long on average?
- [ ] Are boolean names phrased positively and clearly implying true/false?
- [ ] Are loop indices named descriptively when loops are nested or exceed 5 lines?
- [ ] Are qualifiers (`Total`, `Count`, `Average`) consistently positioned as suffixes?
- [ ] Are generic names (`temp`, `data`, `foo`) completely eradicated?
- [ ] Is the name easily searchable across the repository?

---

## 5. Heuristics & Metrics

- **Name Length vs. Scope Ratio**:
  - Local block scope: 4–12 characters.
  - Function / Routine scope: 8–20 characters.
  - Class / Global scope: 12–25 characters.
- **Negative Boolean Smell**: Review double negatives such as `!isNot...`; rewrite them when positive phrasing is clearer.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Cryptic, Negative, and Type-polluted Names
```python
# BAD: Cryptic names, negative boolean, type prefixes, meaningless temp
def calc(arr_data, flag_not_done):
    t = 0 # What is t?
    for i in arr_data:
        if not flag_not_done: # Double negative nightmare!
            t += i.v
    return t
```

### Pattern: Clear, Positive, Problem-Domain Identifiers
```python
# GOOD: Problem-domain names, positive booleans, explicit qualifiers
from typing import Sequence

class InvoiceItem:
    unit_price_cents: int
    is_tax_exempt: bool

def calculate_taxable_subtotal(
    invoice_items: Sequence[InvoiceItem],
    is_processing_active: bool
) -> int:
    if not is_processing_active:
        return 0

    subtotal_cents = 0
    for item in invoice_items:
        if not item.is_tax_exempt:
            subtotal_cents += item.unit_price_cents

    return subtotal_cents
```

---

## 7. Inspection & Verification Protocol

1. Read all variable names out loud: are they pronounceable and natural?
2. Search for any boolean starting with `isNot`, `no`, `disabled`, or `un`: convert to positive equivalents.
3. Check all nested loops for generic index variables: ensure indices describe the dimension (`row`, `col`, `layer`).
