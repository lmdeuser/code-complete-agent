# Code Complete: Agent Engineering Handbook (English Edition)

This directory contains an **unofficial operational adaptation** of Steve McConnell's *Code Complete: A Practical Handbook of Software Construction* (Second Edition), organized for AI coding agents and professional software engineers. It is not the original English text, an official publication, or a replacement for current project requirements.

## Authority and Interpretation

- Applicable system, user, and project instructions always take precedence.
- Existing project architecture, language idioms, formatter settings, test conventions, and acceptance criteria win over this reference.
- **Prescriptive Rules** are context-dependent review guidance, not universal policies.
- **Operational Checklists Derived from McConnell** combine book concepts with modern agent practices; they are not represented as the book's official checklists.
- **Heuristics & Metrics** provide diagnostic signals. Numeric values are not automatic pass/fail gates unless a project explicitly adopts them.
- Modern additions include scope limits, verification provenance, and controls on commits, dependencies, global configuration, and other side effects.

## Structure and Organization

The handbook spans all **7 Parts and 35 Chapters** of the book:

- Each chapter is an independent Markdown document (`01-Chapter-01-...md` through `35-Chapter-35-...md`).
- Navigation links appear at the top of each chapter.
- Chapters 1–34 generally follow this schema:
  1. Executive Summary & Core Principle
  2. Theoretical Foundations & Key Concepts
  3. Prescriptive Rules for AI Agents
  4. Operational Checklist Derived from McConnell
  5. Heuristics & Metrics
  6. Code Patterns & Anti-patterns
  7. Inspection & Verification Protocol
- Chapter 35 is a shorter bibliography and continued-learning guide, so code patterns and numeric metrics are not artificially added.

## How AI Agents Should Query This Handbook

1. **Use targeted lookup**: do not load all 35 chapters into one context by default.
2. Open [00-Table-of-Contents.md](00-Table-of-Contents.md) and select only the chapter relevant to the task:
   - Architecture, prerequisites, and complexity: **Chapters 3–5**
   - Classes and routines: **Chapters 6–7**
   - Errors and assertions: **Chapter 8**
   - Algorithm design and pseudocode: **Chapter 9**
   - Variables and data types: **Chapters 10–13**
   - Conditionals, loops, and complexity: **Chapters 15–19**
   - Testing and debugging: **Chapters 22–23**
   - Refactoring and performance: **Chapters 24–26**
   - Layout and comments: **Chapters 31–32**
3. **Use the opt-in skill for normal agent work**: [../agent_skill/SKILL.md](../agent_skill/SKILL.md) contains the concise router and safe operating rules, with one-level references for progressive disclosure.
4. **Use the standalone operational guide** when a prompt-ready document is needed: [../CODE_COMPLETE_AGENT_INSTRUCTIONS.md](../CODE_COMPLETE_AGENT_INSTRUCTIONS.md).
5. **Use the consolidated edition only for broad search or indexing**: [../CODE_COMPLETE_AGENT_HANDBOOK.md](../CODE_COMPLETE_AGENT_HANDBOOK.md). It is generated from the modular chapters, which are the source of truth.

## Verification Expectations

Before publishing changes to this handbook:

- Check local Markdown links and fenced blocks.
- Parse every Python example with `ast.parse`.
- Regenerate the consolidated file from the 35 modular chapters.
- Verify that examples do not contradict monetary, side-effect, testing, or error-handling guidance.
- Review any numeric threshold to ensure it is presented as a contextual heuristic rather than a universal defect criterion.
