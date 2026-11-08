# Chapter 33: Personal Character

[← Previous: Chapter 32](32-Chapter-32-Self-Documenting-Code.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 34 →](34-Chapter-34-Themes-in-Software-Craftsmanship.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Your personal character as an engineer—or your operational persona as an AI Agent—has a greater impact on software quality than your IQ, programming language, or tooling. The most brilliant programmer whose ego prevents them from admitting mistakes or writing simple code will produce catastrophic systems.

Steve McConnell establishes **Humility** as the supreme virtue of software craftsmanship: *Admit your cognitive limitations*. The historical $7 \pm 2$ cognitive model is a reminder of limited working memory, not a numeric code gate. Engineers should design simple, modular code that reduces simultaneous concepts and avoids convoluted cleverness.

---

## 2. Theoretical Foundations & Key Concepts

### Key Virtues of the Master Craftsman
1. **Humility**:
   - Acknowledge that software is too complex for anyone to comprehend in its entirety.
   - Design code so that no single routine requires tracking more than a handful of moving parts.
   - Write automated tests and assertions because you know you *will* make mistakes.
2. **Curiosity**:
   - Never stop learning. Read books, inspect libraries, study compiler internals, and learn multiple programming paradigms.
3. **Intellectual Honesty**:
   - Admit when you do not understand a problem or tool.
   - Never pretend code works when it hasn't been tested.
   - Ground all engineering claims in verified reality.
4. **Discipline & Good Habits**:
   - Habits are what you fall back on under crunch time and pressure.
   - Disciplined engineers write clean code, format properly, and run unit tests as a natural, non-negotiable reflex.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 33.1 — Absolute Intellectual Honesty**: Never hallucinate API signatures, fake benchmark numbers, or invent file paths. If a fact or requirement is unknown, explicitly state that it is unknown and verify.
2. **RULE 33.2 — Practice Engineering Humility**: Never write overly clever, dense, or obfuscated one-liners to demonstrate algorithmic brilliance. Prefer boring, clean, easily verifiable code.
3. **RULE 33.3 — Ground Claims in Verified Evidence**: When declaring a task complete, provide the actual build and test command outputs. Never report success based merely on intent.
4. **RULE 33.4 — Discipline Under Pressure**: Maintain full engineering standards (types, tests, error checks) even during urgent bug fixes. Quality prevents the panic cycles of bad engineering.

---

## 4. Operational Checklist Derived from McConnell: Personal Character

- [ ] Does the engineer/agent practice humility by designing simple, cognitive-friendly code?
- [ ] Are claims grounded in empirical evidence and actual test outputs?
- [ ] Is cleverness avoided in favor of clarity and maintainability?
- [ ] Is intellectual honesty maintained regarding uncertainties and defects?
- [ ] Are disciplined coding habits maintained consistently across all tasks?

---

## 5. Heuristics & Metrics

- **Cognitive Simplicity**: Can a junior engineer or a smaller AI model understand the routine without external explanation? If not, simplify.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Arrogant, Obfuscated "Cleverness"
```python
# BAD: Arrogant one-liner; unreadable, untestable, impossible to debug
f = lambda l: [x for x in l if x % 2 == 0 and not any(x % i == 0 for i in range(2, int(x**0.5)+1))]
```

### Pattern: Humble, Clear, and Testable Engineering
```python
# GOOD: Humble, readable, easily debugged, and documented
def is_prime(number: int) -> bool:
    """Determines if an integer is prime."""
    if number < 2:
        return False
    for divisor in range(2, int(number ** 0.5) + 1):
        if number % divisor == 0:
            return False
    return True

def extract_even_primes(numbers: list[int]) -> list[int]:
    """Extract even prime numbers from input list."""
    return [num for num in numbers if num % 2 == 0 and is_prime(num)]
```

---

## 7. Inspection & Verification Protocol

1. Audit generated code: is any section written to show off cleverness rather than prioritize clarity?
2. Ensure every claim of completion is backed by verified execution output.
