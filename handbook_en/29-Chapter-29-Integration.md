# Chapter 29: Integration

[← Previous: Chapter 28](28-Chapter-28-Managing-Construction.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 30 →](30-Chapter-30-Programming-Tools.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Integration is the phase of software construction where individual software components are combined and tested as a unified whole. How you choose to integrate components has a profound impact on project risk, debugging time, and delivery predictability.

Steve McConnell strongly favors **incremental integration—exemplified by the Daily Build and Smoke Test—because it usually lowers diagnosis and schedule risk compared with late Big Bang integration**. Integrate frequently, in small increments, and treat a broken build as a priority-zero emergency.

---

## 2. Theoretical Foundations & Key Concepts

### Phased vs. Incremental Integration
- **Phased Integration ("Big Bang")**:
  - Developers write all components in isolation for months, then attempt to assemble everything at the end.
  - *Result*: Simultaneous emergence of dozens of complex inter-module bugs, finger-pointing, impossible debugging, and schedule collapse.
- **Incremental Integration**:
  - Start with a tiny, working skeletal system.
  - Add one component or routine at a time.
  - Test immediately. If the system breaks, the new component and its interactions become the primary investigation scope, though the change may also expose a latent defect elsewhere.

### Incremental Integration Strategies
1. **Top-Down Integration**: Start with high-level user interface and control flow; use stubs for lower-level data layers.
2. **Bottom-Up Integration**: Start with core drivers and data utilities; write test harnesses to verify them before assembling higher layers.
3. **Sandwich Integration**: Combine top-down high-level design with bottom-up foundational components, meeting in the middle.
4. **Feature-by-Feature (Continuous Integration)**: Integrate complete vertical slices of functionality into the main branch continuously.

### The Daily Build and Smoke Test (Precursor to CI/CD)
- Build the entire software system automatically every single day (or on every push).
- Run a **Smoke Test**: an automated suite that exercises all basic features from end to end.
- **The Golden Rule**: *If a shared build or smoke test breaks, restoring it becomes a priority*. Agents should determine whether their change caused it and avoid unrelated fixes without authorization.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 29.1 — Integrate Incrementally**: Implement and integrate components in meaningful verifiable increments rather than waiting for a large final assembly; no fixed module count applies.
2. **RULE 29.2 — Restore Relevant Builds**: If a relevant build or test suite fails, determine ownership and cause before adding dependent features. Do not silently expand into unrelated pre-existing failures.
3. **RULE 29.3 — Run Relevant Smoke Checks**: After modifying a module, run the project checks and integration scenarios that cover the change before declaring success.
4. **RULE 29.4 — Keep Dependencies Build-Ready**: Ensure that newly introduced modules or assets are registered in build manifests (`CMakeLists.txt`, `package.json`, `Cargo.toml`) so the build remains hermetic and automated.

---

## 4. Operational Checklist Derived from McConnell: Integration

- [ ] Is an incremental integration strategy selected over phased "Big Bang" assembly?
- [ ] Is an automated build and smoke test executed continuously?
- [ ] Are broken builds treated as top-priority emergencies?
- [ ] Does the smoke test exercise all major functional areas end-to-end?
- [ ] Are new components integrated in small, easily diagnosable increments?

---

## 5. Heuristics & Metrics

- **Build Frequency**: Continuous integration on every push; local developer builds run within seconds to minutes.
- **Smoke Test Runtime**: Keep feedback fast enough for the team's workflow; the appropriate runtime depends on the system and CI pipeline.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Big Bang Integration Disaster
```text
Phase 1 (Months 1-3): Developer A writes Database; Developer B writes UI; Developer C writes Logic.
Phase 2 (Month 4): Smash all three together.
Outcome: Database expects snake_case, Logic produces camelCase, UI crashes on missing callbacks.
Debugging time: 8 weeks of emergency crunch.
```

### Pattern: Incremental Vertical Slice Integration
```yaml
# GOOD: Modern CI pipeline executing build, lint, and smoke tests on every commit
name: Continuous Integration

on: [push, pull_request]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Environment
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'
      - run: npm ci
      - name: Lint Verification
        run: npm run lint
      - name: Build Subsystems
        run: npm run build
      - name: Smoke & Unit Test Suite
        run: npm run test:smoke
```

---

## 7. Inspection & Verification Protocol

1. Run the project build script: verify zero compilation errors.
2. Run the relevant integration/smoke suite: confirm affected scenarios pass and report unrelated failures separately.
