---
name: starting-with-docs-and-code-discovery
description: Use when a task needs project context before code edits, especially in a new conversation, unfamiliar modules, unclear ownership, or potentially stale docs.
---

# Starting With Docs And Code Discovery

## Overview
Start each task with documentation-grounded discovery. If initial context is wrong, implementation speed only increases wrong edits.

## When to Use
- First turn of a new conversation or task
- Unfamiliar module boundaries or ownership
- Symptoms: "not sure where to edit", "need to patch fast", "docs might be stale"

Do not use this as full design work. Use it as a mandatory first-pass context lock.

## Core Pattern
1. Dispatch a subagent to read docs first:
   - docs/architecture.md
   - docs/knowledges/*.md
   - docs/module/*.md
2. Dispatch a subagent to find relevant code paths (entrypoints, modules, tests).
3. Write a short action map that merges docs findings and code findings before any edit.

## Quick Reference
| Step | Goal | Required Output |
|---|---|---|
| Docs scan | Understand intended system behavior | Paths and key constraints from docs |
| Code scan | Locate real implementation surface | Exact files and likely change points |
| Merge summary | Prevent wrong-file edits | 5-10 line action map with docs/code alignment |

## Implementation
Use prompts that force path-level evidence.

```text
Read docs/architecture.md, docs/knowledges/*.md, and docs/module/*.md.
Return: core constraints, open ambiguities, and exact doc paths cited.
```

```text
Find code implementing [feature/bug area].
Return: exact file paths, key symbols, and test files.
```

## Baseline Failures Found In RED
- Began with direct coding workflow and treated docs review as optional.
- Did not require subagent-driven discovery under time pressure.
- Did not enforce docs+code merged summary before editing.

## Rationalizations And Counters
| Excuse | Reality |
|---|---|
| "I know this module already" | Memory drifts; verify with docs and code evidence. |
| "I'll check docs after patching" | Late docs checks hide wrong assumptions and rework. |
| "Subagent is slower" | Wrong initial targeting costs more than one discovery pass. |

## Red Flags - Stop And Re-run Discovery
- "I can patch this in one quick edit"
- "I'll just grep first"
- "Docs are probably fine"

If any red flag appears: stop, run docs scan subagent, then code scan subagent.

## Common Mistakes
- Reading only architecture docs and skipping knowledge/module docs.
- Returning broad areas instead of exact file paths and symbols.
- Starting edits without publishing the docs+code action map.
