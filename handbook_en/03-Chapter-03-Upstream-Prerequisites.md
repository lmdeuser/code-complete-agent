# Chapter 3: Measure Twice, Cut Once: Upstream Prerequisites

[← Previous: Chapter 2](02-Chapter-02-Metaphors-in-Software-Development.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 4 →](04-Chapter-04-Key-Construction-Decisions.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

The ancient carpentry adage *"Measure twice, cut once"* is the foundation of high-efficiency software engineering. The most common and lethal failure mode in software construction is rushing into coding before upstream prerequisites—**Problem Definition, Requirements Analysis, and System Architecture**—have been adequately satisfied.

Defects introduced in requirements or architecture that leak into construction multiply in remediation cost by orders of magnitude. For an AI Agent, you must conduct an **Upstream Verification Check** before writing code: if requirements are ambiguous, contradictory, or architecturally unsound, stop and resolve the ambiguity rather than guessing or fabricating assumptions.

---

## 2. Theoretical Foundations & Key Concepts

### The Cost-of-Defect Multiplier
Empirical research documented by McConnell, Boehm, and NASA confirms that the cost to detect and repair a defect escalates dramatically across lifecycle phases:
- Requirements phase: **$1x**
- Architecture / High-Level Design: **$3x**
- Construction / Detailed Design: **$5x - $10x**
- System / Acceptance Testing: **$10x - $20x**
- Post-Release / Production: **$10x - $100x+**

### The Three Upstream Prerequisites

1. **Problem-Definition Prerequisite**:
   - A clear, unambiguous statement of the problem being solved without biasing toward a specific implementation.
   - Evaluated from the user's perspective, defining *what* the pain point is, not *how* code will solve it.
2. **Requirements Prerequisite**:
   - Explicit, verifiable specifications of functional and non-functional system behavior.
   - Requirements must be complete, consistent, unambiguous, and testable.
   - Scope stabilization: changes to requirements during construction must be explicitly tracked, estimated, and governed.
3. **Architecture Prerequisite**:
   - The high-level blueprint that partitions the system into clean subsystems and classes.
   - Addresses major technical risks: data storage, concurrency, error handling, security, scalability, performance budgets, third-party library dependencies, and change strategy (information hiding).

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 3.1 — Verify Before Implementation**: Always read existing specification files, interfaces, and configurations before writing code. Never hallucinate requirements or invent architectural components.
2. **RULE 3.2 — Halt on Ambiguity**: When a prompt or user request contains conflicting or impossible requirements, do not silently choose an arbitrary interpretation. State the discrepancy clearly and request clarification or propose the technical alternative.
3. **RULE 3.3 — Architecture Alignment**: Ensure every new class or module conforms to the existing architectural layers and boundaries (e.g., UI layer must not query raw databases; domain models must not import HTTP transport handlers).
4. **RULE 3.4 — Check Non-Functional Requirements**: Verify that non-functional constraints (concurrency, memory consumption, latency limits, security permissions) are known and satisfied before completing construction.

---

## 4. Operational Checklist Derived from McConnell: Upstream Prerequisites

### Requirements Checklist
- [ ] Is the business purpose of the project clearly stated?
- [ ] Are all requirements unambiguous and mutually consistent?
- [ ] Can each requirement be independently tested and verified?
- [ ] Are edge cases and abnormal input conditions specified?
- [ ] Are performance, security, and reliability constraints defined?

### Architecture Checklist
- [ ] Is the overall system architecture clearly divided into modular subsystems?
- [ ] Does the architecture specify clear boundaries and information hiding between modules?
- [ ] Is the error-handling strategy defined uniformly across all layers?
- [ ] Is the data persistence strategy explicitly documented?
- [ ] Does the architecture anticipate anticipated future changes and isolate volatile areas?

---

## 5. Heuristics & Metrics

- **Requirements Volatility**: Track changes and their rework impact. Stabilization and iterative planning are both valid responses; no universal monthly percentage predicts velocity.
- **Architectural Completeness**: Architecture should resolve the decisions needed for current construction, with detail proportional to system risk and scale rather than a fixed percentage.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Coding Without Architectural Invariants
```python
# BAD: Direct coupled database connection and business logic mixed inside transport handler
import sqlite3

def handle_http_request(request):
    conn = sqlite3.connect("prod.db") # Architectural violation: Direct DB access from transport
    cursor = conn.cursor()
    cursor.execute(f"SELECT * FROM users WHERE id = '{request.params['id']}'") # SQL Injection vulnerability
    return cursor.fetchall()
```

### Pattern: Architecture-Conforming Layered Construction
```python
# GOOD: Explicit layered separation (Domain -> Repository -> Transport)
from dataclasses import dataclass
from typing import Protocol, Optional

@dataclass(frozen=True)
class User:
    id: str
    email: str

class UserRepository(Protocol):
    def find_by_id(self, user_id: str) -> Optional[User]:
        ...

class GetUserUseCase:
    def __init__(self, user_repo: UserRepository):
        self._user_repo = user_repo

    def execute(self, user_id: str) -> Optional[User]:
        if not user_id or not user_id.isalnum():
            raise ValueError(f"Invalid user_id format: {user_id!r}")
        return self._user_repo.find_by_id(user_id)
```

---

## 7. Inspection & Verification Protocol

1. Trace the code being written back to an explicit requirement or architectural design decision.
2. Verify that no architectural boundaries (e.g., domain isolation, dependency inversion) are breached.
3. If an upstream prerequisite is missing or contradictory, flag it immediately before continuing.
