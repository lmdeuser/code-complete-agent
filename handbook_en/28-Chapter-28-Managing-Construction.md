# Chapter 28: Managing Construction

[← Previous: Chapter 27](27-Chapter-27-How-Program-Size-Affects-Construction.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 29 →](29-Chapter-29-Integration.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Managing construction is about enforcing discipline, consistency, and realistic estimation. Without managerial rigor during construction, development degenerates into subjective stylistic debates, scope creep, and chaotic code merges.

Steve McConnell establishes that **Good management enables good craftsmanship**: establish coding standards *before* writing code, automate style and quality verification, track progress against objective milestones (passing tests, not claimed completion percentages), and treat configuration management as non-negotiable infrastructure.

---

## 2. Theoretical Foundations & Key Concepts

### 1. Enforcing Coding Conventions
- Coding conventions (naming, layout, file structure) must be established at the inception of the project.
- **Automate Enforcement**: Do not waste human code review time on indentation or brace placement; configure automated formatters (`prettier`, `black`, `rustfmt`, `clang-format`) and strict linters (`ruff`, `eslint`).

### 2. Configuration Management and Version Control
- All source files, build scripts, tests, dependencies, and configuration templates must live in version control.
- Commit hygiene:
  - Commits must be atomic (one cohesive change per commit).
  - Commit messages should follow the repository’s existing convention; Conventional Commits is one option, not a universal requirement.
  - Never commit build artifacts, cache files, temporary files, or unencrypted secrets.

### 3. Verification-Driven Progress Tracking
- Never measure progress by "lines of code written" or developer claims like "I am 90% done."
- Measure progress exclusively by **working, verified, tested code that integrates cleanly into the main build**.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 28.1 — Strictly Follow Existing Conventions**: Follow the established formatting, casing, and directory conventions of the target codebase. Never unilaterally reformat existing code without permission.
2. **RULE 28.2 — Automated Formatting & Linting Pre-flight**: Run the project's automated linter and formatter before delivering any change. Code that fails linter rules is defective.
3. **RULE 28.3 — Secret Hygiene**: Never commit API keys, private certificates, database passwords, or auth tokens to git or log files. Use the project’s approved secret store or environment-variable references without recording secret values.
4. **RULE 28.4 — Focused Commits When Requested**: Keep changes logically cohesive and follow the repository's existing commit-message convention; do not impose Conventional Commits or commit without authorization.

---

## 4. Operational Checklist Derived from McConnell: Managing Construction

- [ ] Are coding conventions established and automatically enforced?
- [ ] Is version control configured with atomic commit discipline?
- [ ] Are build environments hermetic and reproducible?
- [ ] Is progress verified through automated tests rather than subjective assertions?
- [ ] Are secrets, credentials, and build artifacts excluded from source control?

---

## 5. Heuristics & Metrics

- **Commit Granularity**: A cohesive commit may touch as many files as the change requires; follow project review and green-build expectations rather than a fixed file count.
- **Lint Violations**: Follow the project's configured warning policy and address new violations without imposing a new zero-warning CI gate.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Polluting Version Control with Secrets and Build Artifacts
```gitignore
# BAD: Empty or missing .gitignore
# Result: Secrets (.env), node_modules, build binaries, and .pyc files committed to repository
```

### Pattern: Professional Git Hygiene and Pre-commit Discipline
```gitignore
# GOOD: Comprehensive .gitignore isolating build artifacts and secrets
# Environment & Secrets
.env
*.pem
*.key

# Language Artifacts
__pycache__/
*.py[cod]
node_modules/
dist/
build/
target/

# IDE & OS files
.DS_Store
.vscode/settings.json
.idea/
```

---

## 7. Inspection & Verification Protocol

1. Run `git status` or file inspect before completing task: confirm no secrets or untracked temporary files were created.
2. Verify that linter and formatter have run with zero warnings.
