# Chapter 32: Self-Documenting Code

[← Previous: Chapter 31](31-Chapter-31-Layout-and-Style.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 33 →](33-Chapter-33-Personal-Character.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

The primary documentation of a software system is the **Source Code Itself**. Comments that merely repeat what the code says in English are worse than useless: they clutter the screen, decay into falsehoods when code changes, and insult the intelligence of the reader.

A useful rule for comments is: **good code communicates implementation clearly; comments should add intent, rationale, contracts, or helpful high-level structure rather than transliterating syntax**. If code is so convoluted that it requires a paragraph of explanation to decipher its operation, do not write a comment: **Refactor the code**.

---

## 2. Theoretical Foundations & Key Concepts

### The Hierarchy of Self-Documentation
1. **Clean Architecture & Module Names**: A high-level directory structure that reveals domain intent.
2. **Clear Class Names**: Classes named after real-world domain concepts.
3. **Focused Routines with Verb-Noun Names**: Routines whose names state their entire contract.
4. **Descriptive Variable Names**: Eliminating all magic numbers and abbreviations.
5. **Named Constants and Enums**: Turning mystery values into self-explanatory symbols.
6. **Code Layout & Formatting**: Visual structure matching logic.

### When to Write Comments (The "Why", Not the "What")
- **Document Intent**: Explain the business rationale or algorithmic purpose.
- **Document Assumptions**: Preconditions, performance expectations, thread-safety invariants.
- **Document Workarounds**: Explain why a non-obvious approach was taken (e.g. bug in third-party library, OS quirk, hardware hardware errata).
- **Document High-Level Algorithms**: Reference papers, RFCs, or mathematical proofs.

### Anti-patterns in Commenting
- **Restating the Code**: `i = i + 1; // add one to i`. Pure noise.
- **Apologies for Bad Code**: `// This is a terrible hack, fix later`. Fix it now.
- **Outdated / Contradictory Comments**: The comment says one thing, but the code was modified to do the opposite. Lethal for debugging.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 32.1 — Add Information Through Comments**: Avoid comments that merely rephrase syntax. Explain intent, trade-offs, contracts, assumptions, and useful high-level behavior where helpful.
2. **RULE 32.2 — Refactor Instead of Explaining Bad Code**: If a section of code is difficult to understand, improve variable names, extract helper routines, and simplify control flow before resorting to comments.
3. **RULE 32.3 — Mandatory Maintenance of Comments**: When editing existing code, always update or delete associated comments. Leaving stale comments is a critical failure.
4. **RULE 32.4 — Document Edge-Case Workarounds**: When implementing a non-standard workaround for an external bug or environment constraint, write a comment explaining the issue, linking to the ticket or CVE if applicable.

---

## 4. Operational Checklist Derived from McConnell: Self-Documenting Code

- [ ] Does the code explain itself primarily through clear naming and modular design?
- [ ] Do comments focus on *why* the code was written rather than *what* it does?
- [ ] Are all redundant, paraphrasing comments eliminated?
- [ ] Are non-obvious workarounds and external constraints explicitly documented?
- [ ] Are all comments accurate and synchronized with the latest code logic?
- [ ] Are magic numbers replaced with self-documenting named constants?

---

## 5. Heuristics & Metrics

- **Redundant Comment Rate**: 0%. Any comment that simply translates code keywords to English (`// loop over users`, `// return result`) must be deleted.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Redundant Noise Comments and Apologies
```python
# BAD: Restating code, apologizing for bad hacks, out-of-date comments
# set counter to zero
counter = 0

# FIXME: this is a horrible hack because service X drops connections
time.sleep(5) # wait 5 seconds

# check if user is admin
if user.role == 2: # Magic number 2!
    counter += 1 # increment counter
```

### Pattern: Self-Documenting Code with Intent-Revealing "Why" Comments
```python
# GOOD: Code is self-documenting; comments explain non-obvious "why"
from enum import IntEnum

class UserRole(IntEnum):
    StandardUser = 1
    SystemAdministrator = 2

def count_active_administrators(users: list[User]) -> int:
    return sum(
        1
        for user in users
        if user.is_active and user.role == UserRole.SystemAdministrator
    )

def poll_service_with_vendor_cooldown() -> ServiceStatus:
    # Vendor Errata #4082: Service X drops TCP sockets if reconnected within 5 seconds.
    enforce_vendor_socket_cooldown(seconds=5)
    return poll_service_status()
```

---

## 7. Inspection & Verification Protocol

1. Read affected comments: remove mechanical or stale repetition while retaining useful interface and high-level explanations.
2. Verify that every non-obvious algorithm or workaround has a clear "why" explanation.
