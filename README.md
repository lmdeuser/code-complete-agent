# Code Complete Agent

An **unofficial operational adaptation** of Steve McConnell's *Code Complete*, 2nd Edition, designed as optional guidance for AI coding agents and software engineers.

The repository contains no original book text, EPUB, or web-reader application. It is not affiliated with or endorsed by Steve McConnell or the book's publisher.

## Design Goals

- **Explicit opt-in**: the agent skill uses `disable-model-invocation: true` and is invoked only with `/code-complete`.
- **Project first**: system, user, and project instructions, architecture, conventions, and acceptance criteria take precedence.
- **No universal numeric gates**: line counts, complexity, coverage, and similar metrics are review heuristics unless the current project explicitly adopts limits.
- **No hidden side effects**: the skill contains Markdown only—no hooks, scripts, MCP servers, startup changes, or package dependencies.
- **Scoped changes**: it discourages unrelated formatting, refactoring, dependency installation, global configuration changes, commits, pushes, and external access.
- **Verifiable claims**: build, test, benchmark, and integration results must come from real execution and be reported with their limitations.

## Contents

- `agent_skill/` — self-contained opt-in skill with a concise router and progressive references.
- `CODE_COMPLETE_AGENT_INSTRUCTIONS.md` — standalone prompt-ready operational guidance.
- `handbook_en/` — 35 modular chapters with navigation, derived checklists, examples, and heuristics.
- `CODE_COMPLETE_AGENT_HANDBOOK.md` — consolidated searchable edition generated from the modular chapters.

## Skill Structure

- `SKILL.md` — authority, scope, workflow, and reference routing.
- `design.md` — prerequisites, architecture, classes, and pseudocode.
- `construction.md` — routines, variables, data types, control flow, errors, layout, and comments.
- `verification.md` — reviews, testing, debugging, and verification reporting.
- `evolution.md` — refactoring, performance, integration, and tools.
- `heuristics.md` — safe interpretation of numeric guidance and source chapters.

## Installation

The target environment is an agent runtime that discovers skills under `~/.agents/skills/`.

First, verify whether `~/.agents/skills/code-complete/` already exists. Do not overwrite an existing installation. Then copy the skill:

```bash
test ! -e ~/.agents/skills/code-complete || {
  echo "Target already exists; refusing to overwrite it" >&2
  exit 1
}
mkdir -p ~/.agents/skills/code-complete
cp -R agent_skill/. ~/.agents/skills/code-complete/
```

Start a new agent session if the runtime does not refresh its skill registry dynamically. Invoke the skill explicitly:

```text
/code-complete
```

The model cannot auto-invoke this skill from ambient coding tasks. To roll back, remove only `~/.agents/skills/code-complete/`; the repository retains the source copy.

## Using the Handbook

Start with `handbook_en/00-Table-of-Contents.md` and load only the chapter relevant to the task. Use the consolidated file for broad search or RAG indexing rather than loading it into every prompt.

The chapter checklists are operational adaptations, not official checklists from the book. Modern additions include scope control, verification provenance, and safeguards around environment and external side effects.

## Source and Rights

*Code Complete* is by Steve McConnell. These materials are an independent summary and operational interpretation. No license is granted for the original book, and no original book files are distributed here. Unless a license is added explicitly later, no open-source license is implied for this adaptation.
