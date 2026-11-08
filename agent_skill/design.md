# Design and Prerequisites

Derived primarily from *Code Complete*, chapters 1–6 and 9. These are context-dependent design aids, not mandatory architecture templates.

## Before implementation

1. Read applicable project instructions, relevant code, interfaces, manifests, and existing tests.
2. Identify requested behavior, important non-functional constraints, compatibility requirements, and observable acceptance evidence.
3. Resolve ambiguity from project evidence first. Ask only when a missing decision materially changes the result; do not invent requirements.
4. Match rigor to longevity and risk. Do not impose enterprise architecture on a small script or skip essential analysis for a critical component.

## Manage complexity

- Prefer cohesive modules, narrow interfaces, explicit dependencies, and readable data flow.
- Ask which implementation secret a module should hide: a storage format, protocol, third-party API, data structure, or volatile rule.
- Preserve existing abstraction layers and dependency direction. Do not introduce a prescribed Domain/Application/Infrastructure stack into a project that uses another architecture.
- Avoid both tight coupling and speculative decoupling. An interface is useful when it clarifies a real boundary, not merely because a class exists.
- Prefer composition when reuse is the goal. Use inheritance only when substitutability and the base-class contract genuinely hold.
- Initialize objects into valid states and protect invariants using the project's established encapsulation idioms.

## Routine and algorithm design

- Give each routine a clear purpose and an honest name covering meaningful side effects.
- Use intent-level pseudocode or a small desk-check for non-trivial algorithms. Keep only useful high-level comments afterward.
- Group parameters only when they form a coherent domain concept and grouping improves callers.
- Judge routine size by cohesion, nesting, decisions, data flow, and testability—not by an arbitrary line count.

## Design review

- What is the smallest design that satisfies known requirements?
- Which assumptions are verified, and which remain explicit uncertainties?
- Does the change preserve project architecture and public contracts?
- Are new abstractions justified by current behavior or confirmed near-term needs?
- Can the relevant behavior be tested using the project's existing test conventions?
