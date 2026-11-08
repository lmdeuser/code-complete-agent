# Chapter 14: Organizing Straight-Line Code

[← Previous: Chapter 13](13-Chapter-13-Unusual-Data-Types.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 15 →](15-Chapter-15-Using-Conditionals.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Straight-line code consists of statements executed sequentially without branches or loops. While seemingly the simplest code to write, disorganized straight-line code introduces insidious temporal dependencies, masked side effects, and cognitive confusion.

Steve McConnell divides straight-line code into two categories: **code where order matters** (hidden dependencies) and **code where order does not matter**. The central rule is to **make order dependencies explicit and obvious**, while organizing independent code into cohesive, topical paragraphs using the **Principle of Proximity**.

---

## 2. Theoretical Foundations & Key Concepts

### 1. Statements with Mandatory Execution Order
When statements must execute in a strict sequence:
- **Make dependencies obvious**: Pass data explicitly as arguments and return values rather than relying on hidden global side effects.
  - *Bad*: `InitDatabase(); ComputeTotals();` (Did `ComputeTotals` need `InitDatabase`? The signature doesn't say).
  - *Good*: `dbHandle = InitDatabase(); ComputeTotals(dbHandle);`
- **Name routines to document order**: Use routine names that convey sequence (`InitializeAccountingBeforeReport()`).
- **Check return values and preconditions**: Verify each step succeeded before initiating the next dependent action.

### 2. Statements Where Order Does Not Matter
When statements have no mutual dependencies:
- **Principle of Proximity**: Group related statements together into distinct conceptual blocks or "paragraphs".
- **Visual chunking**: Separate distinct statement groups with blank lines.
- **Top-to-bottom reading flow**: Arrange statements so that execution proceeds naturally from top to bottom without requiring the reader to jump back and forth.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 14.1 — Eliminate Hidden Temporal Dependencies**: Never rely on implicit execution order dictated by invisible module-level state. Make every dependency visible in routine signatures (arguments and return values).
2. **RULE 14.2 — Group by Concept (Code Paragraphs)**: Partition straight-line code into logical paragraphs separated by blank lines, adding concise intent comments only when they add information.
3. **RULE 14.3 — Proximity of Operations on the Same Data**: Keep all operations on a specific data structure clustered together. Do not scatter references across an 80-line routine.
4. **RULE 14.4 — Verify Preconditions Sequentially**: If Step B requires Step A, explicitly verify the output of Step A before invoking Step B.

---

## 4. Operational Checklist Derived from McConnell: Organizing Straight-Line Code

- [ ] Are temporal dependencies made explicit through parameter lists and return values?
- [ ] Are routine names descriptive of sequence when order is critical?
- [ ] Are statements grouped into cohesive, logical paragraphs?
- [ ] Are unrelated statements separated by blank lines?
- [ ] Is the code structured to be read linearly from top to bottom?

---

## 5. Heuristics & Metrics

- **Paragraph Cohesion**: Use blank lines to expose logical sub-tasks; do not enforce a fixed number of lines per paragraph.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Interleaved Statements with Hidden Temporal Couplings
```python
# BAD: Interleaved data operations, hidden order dependencies, scattered variables
def generate_report():
    init_graphics()       # Temporal dependency on hardware!
    calc_taxes()          # Operates on global state!
    setup_printer_font()  # Unrelated statement mixed in
    format_output()       # Depends on calc_taxes secretly
    render_screen()       # Depends on init_graphics
```

### Pattern: Explicit Data Flow and Grouped Statements
```python
# GOOD: Explicit data flow, organized in logical paragraphs
from dataclasses import dataclass

@dataclass(frozen=True)
class TaxAssessment:
    taxable_income_cents: int
    tax_due_cents: int

def compute_tax_assessment(gross_income_cents: int, deductions_cents: int) -> TaxAssessment:
    taxable_income_cents = max(0, gross_income_cents - deductions_cents)
    tax_rate_basis_points = 2500 if taxable_income_cents > 5_000_000 else 1500
    tax_due_cents = taxable_income_cents * tax_rate_basis_points // 10_000
    return TaxAssessment(
        taxable_income_cents=taxable_income_cents,
        tax_due_cents=tax_due_cents,
    )
```

---

## 7. Inspection & Verification Protocol

1. Check every sequence of statements: could the statements be reordered without breaking the system? If no, is the dependency made explicit via parameter passing?
2. Ensure related operations on the same object are grouped consecutively.
