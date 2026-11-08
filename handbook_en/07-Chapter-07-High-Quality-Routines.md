# Chapter 7: High-Quality Routines

[← Previous: Chapter 6](06-Chapter-06-Working-Classes.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 8 →](08-Chapter-08-Defensive-Programming.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Routines (functions, methods, procedures) are the fundamental building blocks of computer software. The creation of high-quality routines is the single greatest technique for **reducing complexity**. A well-crafted routine provides an intuitive abstraction that allows a reader to understand what the code does without examining every internal line.

The supreme rule of routine design is **Functional Cohesion**: *A routine must perform one, and only one, operation completely and well*. If a routine does multiple things, it creates cognitive drag, masks edge-case bugs, and frustrates reuse.

---

## 2. Theoretical Foundations & Key Concepts

### Valid Reasons to Create a Routine
1. **Reduce Complexity**: Hide mental detail behind an intuitive name.
2. **Introduce Understandable Abstraction**: Code reads like a high-level specification.
3. **Avoid Code Duplication**: DRY (Don't Repeat Yourself).
4. **Hide Sequence**: Hide the order in which steps occur.
5. **Hide Pointer Operations / Low-Level Details**: Isolate memory management, data packing, or protocol handshakes.
6. **Improve Portability**: Isolate platform-specific system calls.
7. **Simplify Complex Boolean Tests**: Replace `if (age > 65 && !employed && state == 4)` with `if (IsRetireeEligibleForBenefit(person))`.

### The Hierarchy of Cohesion (from Best to Worst)
- **Functional Cohesion (The Ideal)**: Routine performs one single, focused operation (e.g., `ComputeCosine()`, `ValidateEmailAddress()`).
- **Sequential Cohesion**: Steps must occur in sequence, sharing output data (e.g., `FetchDataAndCalculateTaxes()`). Better to split into two routines.
- **Communicational Cohesion**: Operations share the same data but perform independent actions. Split.
- **Temporal Cohesion**: Operations combined simply because they happen at the same time (e.g. `StartupOperations()`). Acceptable only when partitioned into focused sub-routines.
- **Procedural Cohesion**: Steps executed in order without data sharing. Poor.
- **Logical Cohesion**: Routine selects one of several disparate operations via an input flag (`DoOperation(int flag)`). Anti-pattern.
- **Coincidental Cohesion**: Completely unrelated operations grouped together. Total failure.

### Routine Naming Guidelines
- **Functions (return a value)**: Name after the return value or condition: `Cosine()`, `CustomerId()`, `IsFileOpen()`, `CanExecute()`.
- **Procedures (perform actions)**: Name with a strong transitive verb + noun object: `PrintDocument()`, `CalculateSalesTax()`, `SendAlert()`.
- **Accuracy**: The name must describe *everything* the routine does. If the name is `ValidateUser()` but it also saves the user to a database, the name is dishonest or the routine violates SRP.

### Routine Length and Parameters
- **Length**: There is no ideal line range. McConnell discusses evidence that some cohesive routines of 100–200 lines are acceptable and advises caution beyond 200; cohesion and complexity matter more than length.
- **Parameters**: Around seven parameters is a cognitive warning sign. Order parameters consistently with project conventions and avoid surprising mutations of caller-owned inputs.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 7.1 — Functional Cohesion**: Give every routine one clear purpose. Phase comments can reveal extraction opportunities, but cohesive sequential steps do not require mechanical splitting.
2. **RULE 7.2 — Honest Naming**: Routine names must describe all side effects and return values accurately. Never hide database writes or network calls inside a query/getter routine.
3. **RULE 7.3 — Parameter List Review**: When a long parameter list hurts call-site clarity, group values only if they form a coherent domain concept. Follow existing interface conventions.
4. **RULE 7.4 — Parameter Mutation**: Avoid unexpected reassignment or mutation of caller-owned inputs; use the project’s explicit mutability contracts where mutation is intended.
5. **RULE 7.5 — Cyclomatic Complexity Review**: Treat complexity above 10 as a signal to inspect readability and tests, not an automatic requirement to split the routine.

---

## 4. Operational Checklist Derived from McConnell: High-Quality Routines

- [ ] Does the routine have a clear, single purpose (functional cohesion)?
- [ ] Is the routine's name accurate, descriptive, and containing a strong verb/noun pair?
- [ ] Does the routine do everything its name implies and nothing else (no hidden side effects)?
- [ ] Is the routine cohesive and understandable without relying on an arbitrary line limit?
- [ ] Are parameters ordered consistently (Input -> Modifying -> Output)?
- [ ] Is the parameter list clear, with coherent grouping where useful?
- [ ] Are caller-owned inputs protected from unintended mutation?
- [ ] Is elevated cyclomatic complexity reviewed alongside readability and tests?

---

## 5. Heuristics & Metrics

- **Cyclomatic Complexity ($v(G)$)**:
  - 1–5: Simple routine, low risk.
  - 6–10: Moderate complexity, acceptable.
  - 11–20: High complexity, refactoring strongly recommended.
  - \> 20: High-risk control flow that merits focused review and testing; not literally untestable.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Multi-purpose Routine with Hidden Side Effects and Parameter Mutation
```python
# BAD: Mutates parameter, does multiple unrelated things, dishonest name
def check_user(user, send_email_flag):
    # Hidden side effect 1: mutating parameter
    user["checked_at"] = 12345678

    # Hidden side effect 2: network email dispatch inside a check function!
    if send_email_flag:
        send_smtp("welcome", user["email"])

    return user["is_active"]
```

### Pattern: High-Quality Focused Routines
```python
# GOOD: Pure functional cohesion, honest names, immutable arguments
from dataclasses import replace
from typing import Optional

def is_user_eligible_for_activation(user: User) -> bool:
    """Pure query: determines eligibility without side effects."""
    return user.is_registered and not user.is_suspended and user.email_verified

def activate_user(user: User, current_timestamp: int) -> User:
    """Returns a new User instance in active state (immutability)."""
    if not is_user_eligible_for_activation(user):
        raise ValueError(f"User {user.id} is not eligible for activation")
    return replace(user, is_active=True, activated_at=current_timestamp)

def notify_user_of_activation(notifier: NotificationService, user: User) -> None:
    """Explicit procedure for dispatching notifications."""
    notifier.send_templated_email(
        recipient=user.email,
        template_id="account_activated",
        context={"username": user.username}
    )
```

---

## 7. Inspection & Verification Protocol

1. Review long routines for cohesion, decision count, nesting, and data flow rather than applying a fixed length limit.
2. Use decision points and complexity metrics to guide inspection and testing, not as automatic rejection criteria.
3. Verify that parameter mutation is intentional and consistent with the routine contract.
