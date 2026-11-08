# Chapter 31: Layout and Style

[← Previous: Chapter 30](30-Chapter-30-Programming-Tools.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 32 →](32-Chapter-32-Self-Documenting-Code.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Visual layout is not cosmetic: it is the primary interface through which human developers and AI agents perceive software logic. Good layout illuminates the logical structure of a program; poor layout obscures control flow and breeds insidious logic bugs.

Steve McConnell establishes the **Fundamental Theorem of Layout**: *Visual structure must match logical structure*. If two statements are at the same logical level of abstraction or control flow, they must have the same visual alignment. If a block is subordinated inside a loop or conditional, it must be indented consistently.

---

## 2. Theoretical Foundations & Key Concepts

### Why Layout Matters
- Code is read significantly more often than it is written.
- Readers scan visual shapes (indentation, blank lines, grouping) before reading individual characters.
- Inconsistent indentation misleads readers about which statements belong inside blocks.

### Core Layout Principles
1. **Consistent Indentation**: Standardize on 2 or 4 spaces per indentation level. Never mix tabs and spaces.
2. **One Statement per Line**: Never combine multiple statements on a single line (`if (done) return; x = 5;`). Multiple statements per line hide branches from debuggers and readers.
3. **Line Length Limits**: Follow the project formatter and existing file style. Wrap long lines at logical boundaries for readability rather than enforcing a universal column count.
4. **Blank Lines as Paragraph Delimiters**: Use single blank lines to separate logical chunks of straight-line code, and two blank lines between distinct routines or classes.
5. **Brace Placement**: Follow the project’s recognized style consistently within the changed code; do not reformat legacy files merely to impose uniformity.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 31.1 — Strict One Statement Per Line**: Never put multiple statements or an `if` condition and its body on the same line.
2. **RULE 31.2 — Match Existing File Indentation**: Detect whether the file uses 2 spaces, 4 spaces, or tabs; format all new code to match with zero mixed indentation.
3. **RULE 31.3 — Follow Project Line Width**: Wrap long statements and signatures according to the project formatter and surrounding style, not a universal 100-character limit.
4. **RULE 31.4 — Align Parameters and Arguments**: When wrapping long function calls, align parameters vertically or indent continuation lines by double indentation to distinguish them from block bodies.
5. **RULE 31.5 — Use Project Formatters**: Run the formatter already configured by the project on the appropriate changed files; do not add or apply a different formatter globally.

---

## 4. Operational Checklist Derived from McConnell: Layout and Style

- [ ] Does the visual structure of the code directly reflect its logical control flow?
- [ ] Is indentation consistent throughout the entire file?
- [ ] Is there strictly one statement per line?
- [ ] Are long lines wrapped according to project formatting and logical structure?
- [ ] Are logical paragraphs separated by meaningful blank lines?
- [ ] Are complex expressions broken cleanly across operator boundaries?

---

## 5. Heuristics & Metrics

- **Maximum Line Width**: Use the project's configured width and existing file conventions.
- **Statements Per Line**: Prefer one statement per line where that matches language idioms and avoids hidden control flow.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Crammed Statements and Misleading Layout
```c
// BAD: Multiple statements on one line, misleading indentation, broken visual structure
if (isReady) executeTask(); cleanup(); // BUG: cleanup() always runs! Hidden on same line
for (int i=0; i<10; i++) for (int j=0; j<10; j++) matrix[i][j] = 0; // Crammed, hard to debug
```

### Pattern: Clean, Structured, Self-Illuminating Layout
```c
// GOOD: One statement per line, explicit braces, clean visual hierarchy
if (isReady) {
    executeTask();
}
cleanup();

for (int rowIndex = 0; rowIndex < 10; ++rowIndex) {
    for (int colIndex = 0; colIndex < 10; ++colIndex) {
        matrix[rowIndex][colIndex] = 0;
    }
}
```

---

## 7. Inspection & Verification Protocol

1. Check lines against the project width and formatter; wrap where it improves readability and matches local style.
2. Verify that every single-line branch has its body on a new line inside braces.
