# Code Complete (Second Edition) — Agent Handbook Table of Contents

A comprehensive, technical reference handbook and operational manual for AI Agents and software engineers, adapted from Steve McConnell's *Code Complete: A Practical Handbook of Software Construction (Second Edition)*.

---

## Part I: Laying the Foundation
- [Chapter 1: Welcome to Software Construction](01-Chapter-01-Welcome-to-Software-Construction.md) — What construction is, why it is central, and its role as the ultimate source of truth.
- [Chapter 2: Metaphors for a Richer Understanding of Software Development](02-Chapter-02-Metaphors-in-Software-Development.md) — Cognitive models, software accretion, building construction, and scaffolding.
- [Chapter 3: Measure Twice, Cut Once: Upstream Prerequisites](03-Chapter-03-Upstream-Prerequisites.md) — Problem definition, requirements stabilization, architectural validation, and the defect cost multiplier.
- [Chapter 4: Key Construction Decisions](04-Chapter-04-Key-Construction-Decisions.md) — Programming *into* a language, toolchain discovery, quality protocols, and coding standards.

## Part II: Creating High-Quality Code
- [Chapter 5: Design in Construction](05-Chapter-05-Design-in-Construction.md) — Managing complexity as the primary technical imperative, information hiding, loose coupling, and mental chunking.
- [Chapter 6: Working Classes](06-Chapter-06-Working-Classes.md) — Abstract Data Types (ADTs), single responsibility, encapsulation barricades, and containment over inheritance.
- [Chapter 7: High-Quality Routines](07-Chapter-07-High-Quality-Routines.md) — Functional cohesion, honest naming, parameter count limits, and cyclomatic complexity budgets.
- [Chapter 8: Defensive Programming](08-Chapter-08-Defensive-Programming.md) — Protecting against invalid data, assertions vs. exceptions, boundary barricades, and fail-fast offensive programming.
- [Chapter 9: The Pseudocode Programming Process (PPP)](09-Chapter-09-The-Pseudocode-Programming-Process.md) — Designing algorithms in natural language before coding, and self-documenting code iteration.

## Part III: Variables
- [Chapter 10: General Issues in Using Variables](10-Chapter-10-General-Issues-in-Using-Variables.md) — The Principle of Proximity, minimizing scope, span, and live time, and banning variable reuse.
- [Chapter 11: The Power of Variable Names](11-Chapter-11-The-Power-of-Variable-Names.md) — Problem-domain naming, optimal name length (8–20 chars), positive boolean phrasing, and qualifier suffixes.
- [Chapter 12: Fundamental Data Types](12-Chapter-12-Fundamental-Data-Types.md) — Safe integer math, banning float equality comparisons, strict currency types, enumerations, and eliminating magic numbers.
- [Chapter 13: Unusual Data Types](13-Chapter-13-Unusual-Data-Types.md) — Structs/records, pointer safety with RAII and smart pointers, and eradicating mutable global state.

## Part IV: Statements
- [Chapter 14: Organizing Straight-Line Code](14-Chapter-14-Organizing-Straight-Line-Code.md) — Making temporal dependencies explicit, code paragraphs, and grouping operations on identical data.
- [Chapter 15: Using Conditionals](15-Chapter-15-Using-Conditionals.md) — Nominal path first, guard clauses for early returns, handling default cases, and limiting nesting to $\le 3$ levels.
- [Chapter 16: Controlling Loops](16-Chapter-16-Controlling-Loops.md) — Favoring iterators, avoiding accidental index mutation, and bounding external waits or uncertain convergence.
- [Chapter 17: Unusual Control Structures](17-Chapter-17-Unusual-Control-Structures.md) — Pragmatic guard-clause returns, bounded recursion depth, and eradicating `goto`.
- [Chapter 18: Table-Driven Methods](18-Chapter-18-Table-Driven-Methods.md) — Replacing complex conditional ladders with direct, indexed, and stair-step lookup tables.
- [Chapter 19: General Control Issues](19-Chapter-19-General-Control-Issues.md) — McCabe cyclomatic complexity as a review signal, project-consistent blocks, and short-circuit boolean safety.

## Part V: Code Improvements
- [Chapter 20: The Software-Quality Landscape](20-Chapter-20-The-Software-Quality-Landscape.md) — Internal vs. external qualities, quality synergies, and how high quality accelerates delivery speed.
- [Chapter 21: Collaborative Construction](21-Chapter-21-Collaborative-Construction.md) — Peer inspections, checklist-driven audits, small review batches, and egoless engineering.
- [Chapter 22: Developer Testing](22-Chapter-22-Developer-Testing.md) — Structured basis testing, boundary value analysis, equivalence partitioning, and hermetic unit test suites.
- [Chapter 23: Debugging](23-Chapter-23-Debugging.md) — The Scientific Debugging Method: stabilize, hypothesize, isolate root cause, fix, and verify with regression tests.
- [Chapter 24: Refactoring](24-Chapter-24-Refactoring.md) — Code smells, small incremental steps, testing after every transformation, and zero behavioral drift.
- [Chapter 25: Code-Tuning Strategies](25-Chapter-25-Code-Tuning-Strategies.md) — The Pareto principle as a historical heuristic, disciplined optimization, and before/after measurement.
- [Chapter 26: Code-Tuning Techniques](26-Chapter-26-Code-Tuning-Techniques.md) — Hoisting loop invariants, unswitching conditionals, logic short-circuiting, and empirical benchmarking.

## Part VI: System Considerations
- [Chapter 27: How Program Size Affects Construction](27-Chapter-27-How-Program-Size-Affects-Construction.md) — Non-linearity of scale ($O(n^2)$ communication channels), avoiding cyclical dependencies, and subsystem partitioning.
- [Chapter 28: Managing Construction](28-Chapter-28-Managing-Construction.md) — Automated coding conventions, version control hygiene, atomic commits, and secret management.
- [Chapter 29: Integration](29-Chapter-29-Integration.md) — Banning "Big Bang" integration, incremental assembly, and treating broken daily builds as priority-zero emergencies.
- [Chapter 30: Programming Tools](30-Chapter-30-Programming-Tools.md) — Toolchain discovery, static analysis integration, custom test jigs, and hermetic execution environments.

## Part VII: Software Craftsmanship
- [Chapter 31: Layout and Style](31-Chapter-31-Layout-and-Style.md) — Visual layout reflecting logical structure, readable statements, and project-consistent line wrapping.
- [Chapter 32: Self-Documenting Code](32-Chapter-32-Self-Documenting-Code.md) — Commenting the "why" instead of the "what", refactoring instead of explaining bad code, and updating comments.
- [Chapter 33: Personal Character](33-Chapter-33-Personal-Character.md) — Engineering humility, cognitive chunk limits ($7 \pm 2$), intellectual honesty, and grounding claims in verified reality.
- [Chapter 34: Themes in Software Craftsmanship](34-Chapter-34-Themes-in-Software-Craftsmanship.md) — Conquering complexity as the supreme imperative, writing for people first, and programming into the language.
- [Chapter 35: Where to Find More Information](35-Chapter-35-Where-to-Find-More-Information.md) — Classic software literature, continuous learning, and consulting primary authoritative documentation.

---

## Instructions and Supplementary Documents
- [Agent Operational Instructions (`CODE_COMPLETE_AGENT_INSTRUCTIONS.md`)](../CODE_COMPLETE_AGENT_INSTRUCTIONS.md) — Concise, prompt-ready ruleset for AI Coding Agents.
- [Consolidated Handbook (`CODE_COMPLETE_AGENT_HANDBOOK.md`)](../CODE_COMPLETE_AGENT_HANDBOOK.md) — Complete single-file reference edition.
