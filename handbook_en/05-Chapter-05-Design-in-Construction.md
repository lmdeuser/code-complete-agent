# Chapter 5: Design in Construction

[← Previous: Chapter 4](04-Chapter-04-Key-Construction-Decisions.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 6 →](06-Chapter-06-Working-Classes.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

The primary technical imperative of software construction is **Managing Complexity**. Edsger Dijkstra famously pointed out that our intellectual capacity is strictly limited; software is inherently the most complex human endeavor because it lacks physical constraints. If an engineer or AI agent fails to manage complexity, the system will rapidly exceed cognitive grasp, resulting in unpredictable bugs, fragile dependencies, and paralysis.

Design during construction is not an academic exercise: it is an iterative, heuristic process aimed at creating simple, modular, and loosely coupled structures that minimize cognitive load. **Simplicity is the supreme design virtue.**

---

## 2. Theoretical Foundations & Key Concepts

### Key Design Challenges
1. **Design is a "Wicked Problem"**: Horst Rittel and Melvin Webber defined a wicked problem as one that can only be clearly defined by solving it (e.g. the Tacoma Narrows bridge). In software, you must partially construct a solution to understand the hidden dimensions of the problem.
2. **Design is Sloppy**: The final design looks tidy, but the process of discovering it is iterative, full of dead ends, experiments, and trade-offs.
3. **Design is About Trade-offs**: Speed vs. memory, development time vs. maintainability, flexibility vs. simplicity.

### Key Design Principles for Managing Complexity
- **Minimal Complexity**: The cheapest code to write and maintain is simple code. Avoid clever, obfuscated tricks.
- **Ease of Maintenance**: Write code so that a developer unfamiliar with the system can understand it immediately.
- **Loose Coupling**: Connect modules via minimal, clean, and explicit interfaces. Avoid hidden dependencies, shared mutable state, or internal knowledge leakage.
- **Extensibility**: Design areas likely to change so they can be modified without altering surrounding code (Information Hiding).
- **High Fan-In, Low Fan-Out**: Strive for routines that are reused widely (high fan-in) and call few external routines (low fan-out, ideally <= 5-7).
- **Stratification**: Keep abstraction layers strictly segregated. A high-level business routine must not talk directly to hardware registers or low-level sockets.

### Information Hiding (David Parnas)
Information hiding is the single most powerful architectural concept in software engineering:
- Ask: *"What secrets should this class or routine hide from the rest of the program?"*
- Hide: File formats, network protocols, concurrency mechanisms, data structure implementations, and complex business calculations behind stable interfaces.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 5.1 — Complexity Budget**: Avoid classes and routines with unrelated responsibilities. When variable interactions are hard to trace, review cohesion and data flow before deciding whether to split the unit.
2. **RULE 5.2 — Strict Information Hiding**: Hide all internal state, data structures, and implementation choices. Expose only what is strictly necessary through minimal public APIs.
3. **RULE 5.3 — Isolate Areas Likely to Change**: Identify volatile elements (business rules, third-party APIs, hardware interactions, data storage formats) and encapsulate them behind interfaces.
4. **RULE 5.4 — Avoid Premature Flexibility**: Do not add speculative abstractions or over-engineered frameworks for hypothetical future needs (YAGNI). Solve the concrete problem with the simplest robust design.

---

## 4. Operational Checklist Derived from McConnell: Design in Construction

- [ ] Does the design achieve minimal complexity (no gratuitous cleverness)?
- [ ] Is the primary technical imperative of managing complexity respected?
- [ ] Are abstraction levels clean and distinct across architectural strata?
- [ ] Has information hiding been applied to protect volatile implementation details?
- [ ] Are classes and routines loosely coupled and highly cohesive?
- [ ] Has the design identified and isolated components likely to change?
- [ ] Does the design avoid speculative abstractions and over-engineering?

---

## 5. Heuristics & Metrics

- **Cognitive Chunking (Miller's Law)**: The historical $7 \pm 2$ model is a reminder to reduce simultaneous concepts, not a numeric interface limit.
- **Fan-Out Metric**: High fan-out can make behavior difficult to trace. Inspect actual dependencies and cohesion rather than enforcing a universal call count.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Leaking Implementation Secrets (Violating Information Hiding)
```python
# BAD: Client code knows that orders are stored in a raw SQLite table and SQL query details
class OrderManager:
    def __init__(self, db_conn):
        self.db_conn = db_conn # Exposing DB directly

def print_total(order_mgr, user_id):
    # Secret leaked: SQL schema and DB engine logic are spread into the consumer
    cursor = order_mgr.db_conn.cursor()
    cursor.execute(f"SELECT SUM(price) FROM orders WHERE customer_id = ?", (user_id,))
    total = cursor.fetchone()[0]
    print(f"Total: {total}")
```

### Pattern: Robust Information Hiding and Stratification
```python
# GOOD: The repository encapsulates all persistence mechanics behind a domain contract
from decimal import Decimal
from typing import Protocol

class OrderRepository(Protocol):
    def calculate_customer_total(self, customer_id: str) -> Decimal:
        """Calculate total monetary amount spent by customer."""
        ...

class CustomerInvoiceService:
    def __init__(self, orders: OrderRepository) -> None:
        self._orders = orders

    def generate_invoice_summary(self, customer_id: str) -> str:
        total = self._orders.calculate_customer_total(customer_id)
        return f"Customer {customer_id} Total: ${total:.2f}"
```

---

## 7. Inspection & Verification Protocol

1. Audit every public class and routine: does it leak internal data structures (e.g. raw arrays, internal database connections, proprietary file pointers)?
2. Verify that modules interact through narrow, well-defined parameter lists.
3. Check that the design is as simple as possible while satisfying all requirements.
