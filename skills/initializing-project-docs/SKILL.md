---
name: initializing-project-docs
description: Use when docs/design/architecture.md is missing and the user wants a complete, detailed project-docs baseline before coding.
---

# Initializing Project Docs

## Overview
Initialize a complete, detailed docs baseline that can be used immediately for implementation decisions.

For medium-to-large repositories, split independent docs work across multiple subagents to speed up delivery and keep the main session context clean.

## When to Use
- docs/design/architecture.md is missing
- User explicitly requested docs initialization
- Project context is needed before safe implementation
- The task spans multiple modules/topics and docs work can be partitioned independently

Do not use this skill for incremental docs sync after code changes. Use superpowers:maintaining-docs-sync for that.

## Core Pattern
1. Confirm initialization scope with the user in the current session.
2. Define coverage targets first:
   - high-impact modules
   - cross-cutting knowledge topics, official docs, web research, and user-provided facts
   - required contracts and runtime flows
3. Create required docs structure:
   - docs/design/architecture.md
   - docs/knowledge/<topic>.md
   - docs/module/<module>.md
4. Enforce canonical path naming before writing content:
   - Use `docs/knowledge/` only.
   - Never create `docs/knowledges/`.
   - If both exist, consolidate into `docs/knowledge/` and remove plural-path usage.
5. Choose execution mode:
   - Small scope: write docs in the current session.
   - Larger scope: dispatch multiple subagents for independent module/topic docs.
6. Integrate outputs into a single consistent docs set in the current session.
7. Verify completeness, cross-file consistency, and canonical path usage before proceeding.

## Quick Reference
| Step | Action | Output |
|---|---|---|
| Scope confirm | Lock what to document first | Clear module/topic scope |
| Coverage plan | Define module/topic partitions | Explicit docs coverage map |
| Evidence gather | Read code/tests/config/scripts | Source-file-backed facts |
| Structure create | Add required docs files | Baseline docs tree exists |
| Parallel drafting | Optional subagents for independent docs | Faster drafts with cleaner main context |
| Integration and QA | Merge + verify links/terminology/contracts | Complete and internally coherent docs |

## Implementation
Use this checklist to generate full docs (no placeholders):

```text
1) Confirm docs-init scope and priority modules/topics.
2) Build a coverage map before writing:
   - 1 design/architecture doc
   - knowledge docs for key cross-cutting topics, official docs, web research, and user-provided facts
   - module docs for high-impact modules
3) Lock canonical directories:
   - docs/design/architecture.md
   - docs/knowledge/<topic>.md (singular directory only)
   - docs/module/<module>.md
   - forbid docs/knowledges/<topic>.md
4) Gather evidence from code/tests/config/scripts.
5) Draft docs with concrete details (no TBD/TODO/"...").
6) Optional: dispatch multiple subagents in parallel for independent module/topic drafts.
7) Integrate all drafts in current session and enforce consistent terminology.
8) Validate paths and resolve conflicts:
   - if both docs/knowledge and docs/knowledges exist, merge to docs/knowledge
   - update references that point to docs/knowledges/*
9) Verify every major claim has source-file evidence and assumptions are explicit.
10) Produce a completeness report (coverage, unknowns, follow-ups).
```

Parallel subagent rules:

```text
1) Use one subagent per independent partition (topic or module).
2) Give each subagent strict scope and target file paths.
3) Do not let multiple subagents edit the same target file.
4) Keep docs/design/architecture.md synthesis in the main session for one consistent system narrative.
```

**REQUIRED SUB-SKILL:** Use superpowers:dispatching-parallel-agents when docs init can be partitioned safely.

Standard section skeletons are in:
`skills/initializing-project-docs/docs-init-template.md`

## Example: Initializing Docs for a Plugin System

**Scope:** Medium-size codebase (plugins/ directory, 3 core modules)

**Step 1 - Scope Confirmation:** Document plugin architecture, integration points, and API contracts.

**Step 2 - Coverage Map:**
- Design: System architecture, plugin lifecycle, event flow
- Knowledge: Plugin framework documentation, third-party dependencies
- Module: plugin-manager.ts, loader.ts, extension-registry.ts

**Step 3 - Gather Evidence:** Read `plugins/*.ts`, `tests/plugin*.test.ts`, `package.json` (dependencies)

**Step 4 - Output:**
```
docs/design/architecture.md
  - Plugin System Overview
  - Extension Points
  - Event Lifecycle
  - Dependency Contracts

docs/knowledge/plugin-framework-docs.md
  - Framework concepts
  - Official reference links
  - Third-party APIs used

docs/module/plugin-manager.md
  - PluginManager class contract
  - Entry points
  - Configuration options

docs/module/loader.ts.md
  - Loader implementation details
  - Error handling paths
```

**Result:** Complete docs baseline enables immediate architectural discussions and implementation decisions.

## Baseline Failures Found In RED
- Initialization and sync were mixed in one skill, causing wrong workflow branches.
- Single-thread docs initialization slowed delivery on larger repositories.
- Lacking partition rules caused either context bloat or overlapping edits.
- Missing docs was sometimes deferred instead of being initialized immediately.

## Rationalizations And Counters
| Excuse | Reality |
|---|---|
| "I can skip docs for now" | Missing docs blocks safe project-context decisions. |
| "I should use sync skill for init" | Sync skill assumes docs already exist; initialize first. |
| "One giant pass is always simpler" | Large scope benefits from partitioned parallel drafts and main-session integration. |
| "Parallel subagents will always conflict" | Conflicts are avoidable with strict partition boundaries and file ownership. |

## Red Flags - Stop And Initialize
- "Let's code first, docs later"
- "We only need architecture, skip other docs"
- "I'll add placeholders and fill later"
- "One agent should do everything in one giant context"

Any red flag means complete first-version docs before proceeding.

## Common Mistakes
- Creating only docs/design/architecture.md and skipping knowledge/module docs.
- Creating both `docs/knowledge/` and `docs/knowledges/` instead of using only `docs/knowledge/`.
- Writing generic docs without concrete source-file evidence.
- Leaving placeholders (TBD/TODO/...) in final docs.
- Dispatching subagents without independent partitions, causing overlap and churn.
- Using this skill for post-implementation sync updates.
