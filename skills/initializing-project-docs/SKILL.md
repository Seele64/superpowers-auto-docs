---
name: initializing-project-docs
description: Use when docs/architecture.md is missing and the user wants a complete, detailed project-docs baseline before coding.
---

# Initializing Project Docs

## Overview
Initialize a complete, detailed docs baseline that can be used immediately for implementation decisions.

For medium-to-large repositories, split independent docs work across multiple subagents to speed up delivery and keep the main session context clean.

Default workflow for medium-to-large scope: subagents draft `docs/design/*.md` and `docs/modules/*.md` first, then main session synthesizes `docs/architecture.md` last.

## When to Use
- docs/architecture.md is missing
- User explicitly requested docs initialization
- Project context is needed before safe implementation
- The task spans multiple modules/topics and docs work can be partitioned independently

Do not use this skill for incremental docs sync after code changes. Use superpowers:maintaining-docs-sync for that.

## Core Pattern
1. Confirm initialization scope with the user in the current session.
2. Define coverage targets first:
   - design/spec topics (what and why)
   - high-impact modules
   - external knowledge/reference topics
   - required contracts and runtime flows
3. Run architecture reconnaissance before any subagent dispatch:
   - Read code/tests/config/scripts to identify real module boundaries.
   - Build a partition map: each partition has source files, target docs paths, and owner.
   - If boundaries are unclear or overlapping, do not dispatch subagents.
4. Lock minimum output quotas before drafting:
   - At least 1 `docs/design/*.md`
   - At least 1 `docs/modules/*.md`
   - `docs/knowledge/*.md` is optional and only allowed when external references are actually used
5. Create required docs structure:
   - docs/architecture.md
   - docs/design/<topic>.md
   - docs/modules/<module>.md
   - docs/knowledge/<topic>.md
6. Enforce canonical path naming before writing content:
   - Use `docs/modules/` (plural) only.
   - Never create legacy singular module-path variants.
   - Use `docs/knowledge/` (singular) only.
   - Never create legacy plural knowledge-path variants.
   - If old/new variants coexist, consolidate to canonical paths and remove non-canonical usage.
   - Author documentation content in English; allow non-English only when quoting source material.
7. Choose execution mode:
   - Small scope: write docs in the current session, but still draft design/modules before architecture synthesis.
   - Medium-to-large scope with clear partitions: dispatch subagents for design/modules first, then synthesize architecture in main session.
   - If medium-to-large scope has unclear boundaries: do not dispatch yet; clarify boundaries first.
8. Integrate outputs into a single consistent docs set in the current session.
9. Verify completeness, cross-file consistency, and canonical path usage before proceeding.

## Quick Reference
| Step | Action | Output |
|---|---|---|
| Scope confirm | Lock what to document first | Clear module/topic scope |
| Coverage plan | Define module/topic partitions | Explicit docs coverage map |
| Evidence gather | Read code/tests/config/scripts | Source-file-backed facts |
| Structure create | Add required docs files | Baseline docs tree exists |
| Parallel drafting | Mandatory for medium-to-large scope with clear partitions; skip when boundaries are unclear | Faster drafts with cleaner main context and controlled risk |
| Integration and QA | Merge + verify links/terminology/contracts | Complete and internally coherent docs |

## Implementation
Use this checklist to generate full docs (no placeholders):

```text
1) Confirm docs-init scope and priority modules/topics.
2) Build a coverage map before writing:
   - design docs for feature intent, tradeoffs, and constraints (what/why); minimum count: 1
   - module docs for high-impact modules (how); minimum count: 1
   - 1 architecture doc (synthesized after design/modules drafts)
   - knowledge docs for external references and operational notes; optional, only when reference sources exist
3) Build and validate partition map before dispatch:
   - each partition maps to explicit source files and explicit target docs paths
   - no file overlap between partitions
   - each partition has enough evidence to write concrete docs
   - if any partition is ambiguous, skip dispatch and write in main session
4) Lock canonical directories:
   - docs/architecture.md
   - docs/design/<topic>.md
   - docs/modules/<module>.md (plural directory only)
   - docs/knowledge/<topic>.md (singular directory only)
   - forbid all non-canonical legacy path variants
5) Gather evidence from code/tests/config/scripts.
6) Draft docs with concrete details (no TBD/TODO/"...").
7) For medium-to-large scope with clear partitions, dispatch subagents in parallel to draft design/modules.
8) Integrate subagent outputs in current session and enforce consistent terminology.
9) Synthesize docs/architecture.md in current session from consolidated design/modules outputs.
10) Validate paths and resolve conflicts:
   - if legacy module-path variants coexist with canonical module paths, merge into canonical module paths
   - if legacy knowledge-path variants coexist with canonical knowledge paths, merge into canonical knowledge paths
   - update references that point to any legacy path variants
11) Enforce output-balance and workflow-order gates (hard fail if violated):
   - fail if no docs/design/*.md generated
   - fail if no docs/modules/*.md generated
   - fail if medium-to-large scope skips subagent draft phase despite clear partitions
   - fail if architecture synthesis happens before design/modules evidence exists
   - fail if generated docs contain non-English narrative text (except quoted source material)
   - fail if knowledge docs are generated without external sources
   - fail if any knowledge doc lacks related design/modules links
   - fail if knowledge docs outnumber design docs unless user explicitly requested reference-heavy bootstrap
12) Verify every major claim has source-file evidence and assumptions are explicit.
13) Produce a completeness report (coverage, unknowns, follow-ups).
```

Parallel subagent rules:

```text
1) Use one subagent per independent partition (topic or module).
2) Define partitions only after architecture reconnaissance and file-level evidence mapping.
3) Give each subagent strict scope and target file paths.
4) Do not let multiple subagents edit the same target file.
5) If boundaries are unclear, do not dispatch; complete init in the main session.
6) Keep docs/architecture.md synthesis in the main session for one consistent system narrative.
7) Do not ask subagents to author docs/architecture.md.
```

Subagent task prompt template (standard):

```text
You are a docs-initialization subagent.

Scope:
- Partition: [PARTITION_NAME]
- Source files (read-only evidence set):
   - [path/to/source1]
   - [path/to/source2]
- Allowed target docs paths (edit only these):
   - docs/design/[topic].md
   - docs/modules/[module].md

Task:
Draft first-version docs for this partition only.

Hard constraints:
1) Do NOT edit docs/architecture.md.
2) Do NOT edit docs outside the allowed target paths.
3) Every major claim must be backed by source-file evidence.
4) No placeholders (TBD/TODO/"...").
5) Write all documentation narrative in English.
6) Keep terminology consistent with existing docs.
7) If evidence is insufficient, state explicit assumptions and unknowns.

Output format:
=== Patch Summary ===
- <what was documented>

=== Files Edited ===
- docs/design/[topic].md
- docs/modules/[module].md

=== Evidence Map ===
- Claim: <claim>
   Evidence: [file:line]
- Claim: <claim>
   Evidence: [file:line]

=== Assumptions / Unknowns ===
- <assumption or unknown>

=== Boundaries Check ===
- Edited only allowed files: yes/no
- docs/architecture.md untouched: yes/no
```

**REQUIRED SUB-SKILL:** Use superpowers:dispatching-parallel-agents when docs init can be partitioned safely.

Standard section skeletons are in:
`skills/initializing-project-docs/docs-init-template.md`

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
| "Leadership needs architecture in 20 minutes, write it first from assumptions" | Architecture-first without design/modules evidence creates drift and rework; produce evidence-backed drafts first, then synthesize architecture. |

## Red Flags - Stop And Initialize
- "Let's code first, docs later"
- "We only need architecture, skip other docs"
- "Write architecture first and backfill design/modules later"
- "I'll add placeholders and fill later"
- "One agent should do everything in one giant context"

Any red flag means complete first-version docs before proceeding.

## Common Mistakes
- Creating only docs/architecture.md and skipping design/modules/knowledge docs.
- Writing docs/architecture.md first from assumptions in medium-to-large scope.
- Dispatching subagents before understanding real module boundaries.
- Generating many knowledge docs while producing zero design docs.
- Treating knowledge docs as mandatory output instead of reference-conditional output.
- Mixing canonical paths with legacy singular/plural variants.
- Writing generic docs without concrete source-file evidence.
- Leaving placeholders (TBD/TODO/...) in final docs.
- Dispatching subagents without independent partitions, causing overlap and churn.
- Using this skill for post-implementation sync updates.
