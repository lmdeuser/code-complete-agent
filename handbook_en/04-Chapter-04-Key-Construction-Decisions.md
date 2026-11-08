# Chapter 4: Key Construction Decisions

[← Previous: Chapter 3](03-Chapter-03-Upstream-Prerequisites.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 5 →](05-Chapter-05-Design-in-Construction.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Before laying a single brick of code, construction engineers must establish the technical infrastructure, language conventions, quality practices, and tooling environment. A common engineering error is treating programming language selection, code style conventions, and development practices as incidental matters. In reality, these **foundational decisions establish the physical laws of your project**.

For an AI Agent, you must understand the distinction between **"Programming in a Language"** (being constrained by syntax quirks and weak conventions) versus **"Programming into a Language"** (bringing superior software engineering principles and bending the language idioms to maintain robust, readable architecture).

---

## 2. Theoretical Foundations & Key Concepts

### Programming *In* vs. *Into* a Language
- **Programming IN a Language**: The programmer lets the syntax and primitives of the language dictate design thinking. If the language lacks built-in enums or strict types, the programmer resorts to raw magic strings and unverified dictionaries.
- **Programming INTO a Language**: The engineer designs the cleanest, most robust architectural solution conceptually, and then uses the language's capabilities (or constructs appropriate abstractions, types, and assertions) to enforce that design cleanly.

### Establishing Construction Practices
1. **Conventions and Style Guidelines**:
   - Naming conventions, indentation, file organization, and layout must be standardized *before* coding begins.
   - Uniform style eliminates cognitive overhead during code reviews and prevents meaningless churn in git diffs.
2. **Quality Assurance Protocols**:
   - Automated testing suites (unit, integration, regression).
   - Static analysis, linters, and strict compiler warnings (`-Wall -Wextra -Werror`, `mypy --strict`, `eslint --max-warnings 0`).
   - Collaborative peer reviews or automated pre-commit gates.
3. **Tool Selection and Integration**:
   - Modern version control with disciplined branching/rebase strategies.
   - Hermetic build environments (virtualenvs, containers, lockfiles).
   - Continuous integration engines to execute tests and checks on every commit.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 4.1 — Respect Existing Toolchain**: Discover the actual project toolchain from manifest files (`package.json`, `pyproject.toml`, `Cargo.toml`, `CMakeLists.txt`) before executing commands. Never assume tools exist.
2. **RULE 4.2 — Program INTO the Language**: Use the language and project type system to express important invariants, without imposing a new strictness or immutability regime on existing code.
3. **RULE 4.3 — Match Project Conventions**: Follow the existing naming style (camelCase, snake_case, PascalCase) and directory layout of the target project. Never reformat unrelated files or impose conflicting personal style choices.
4. **RULE 4.4 — Compiler / Linter Hygiene**: Run the project’s configured checks and address warnings introduced by the change. Do not enable stricter global warning policies without authorization.

---

## 4. Operational Checklist Derived from McConnell: Key Construction Decisions

- [ ] Has the implementation language been chosen based on problem fitness rather than programmer whim?
- [ ] Are language-specific best practices, idioms, and security guidelines established?
- [ ] Have coding standards (naming, layout, comments) been agreed upon and enforced?
- [ ] Are compiler/linter warnings enabled at the highest practical warning level?
- [ ] Is an automated, repeatable build and test pipeline configured?
- [ ] Has the team committed to "programming into the language" rather than merely "in it"?

---

## 5. Heuristics & Metrics

- **Linters and Typecheckers**: Follow the project’s configured strictness and suppression policy. New strict modes are separate architectural or tooling changes, not prerequisites for every edit.
- **Dependency Hygiene**: Evaluate third-party dependencies against maintenance status, licensing, CVE vulnerability databases, and dependency tree depth. Do not introduce dependencies for trivial tasks.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Programming "In" a Language (Passively Accepting Weak Primitives)
```javascript
// BAD: Programming "in" JavaScript: using raw strings, no validation, loose comparisons
function setStatus(user, status) {
  if (status == 1) { // Magic number
    user.status = 'active';
  } else if (status == 2) {
    user.status = 'inactive';
  }
}
```

### Pattern: Programming "Into" a Language (Enforcing Strong Invariants)
```typescript
// GOOD: Programming "into" TypeScript: exhaustive enums, immutability, runtime assertion
export enum UserStatus {
  Active = 'ACTIVE',
  Inactive = 'INACTIVE',
  Suspended = 'SUSPENDED',
}

export interface UserRecord {
  readonly id: string;
  readonly status: UserStatus;
}

export function updateUserStatus(user: UserRecord, newStatus: UserStatus): UserRecord {
  if (!Object.values(UserStatus).includes(newStatus)) {
    throw new TypeError(`Unknown user status: ${newStatus}`);
  }
  return { ...user, status: newStatus };
}
```

---

## 7. Inspection & Verification Protocol

1. Verify that all newly created code complies with the project's explicit style guidelines and lint configurations.
2. Verify that language features are used safely with full type annotations.
3. Confirm that no global dependencies or unvetted external libraries were introduced.
