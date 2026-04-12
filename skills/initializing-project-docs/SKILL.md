---
name: initializing-project-docs
description: Use when docs are missing or existing docs have grown dense with mixed module or design concerns and need evidence-backed split expansion.
---

# Initializing Project Docs

## Overview
Initialize or refresh a complete docs baseline from code evidence.

Core principle: when docs grow, split by independent component or subdomain and preserve detail by redistributing it across multiple docs. Do not "clean up" by deleting depth.
If `docs/superpowers/plans` or `docs/superpowers/specs` exists, review them as required context before drafting or refreshing docs.

For medium-to-large repositories, split independent docs work across multiple subagents to speed up delivery and keep the main session context clean.

## When to Use
- docs/architecture.md is missing
- docs exist but no longer match current codebase behavior or structure
- module docs keep accumulating multiple independent components in one file
- design docs keep accumulating multiple independent subdomains in one file
- docs updates are repeatedly blocked by giant files and merge conflicts
- User explicitly requested docs initialization
- User asked to "re-initialize" docs even though docs already exist
- Project context is needed before safe implementation
- The task spans multiple modules/topics and docs work can be partitioned independently
- Historical plan/spec context exists under `docs/superpowers/plans` or `docs/superpowers/specs`

Do not use this skill for small end-of-task sync checks. Use superpowers:maintaining-docs-sync for completion-gate verification.

## Core Pattern
1. Confirm docs scope with the user in the current session.
2. Run preflight mode selection:
   - If docs/architecture.md is missing: bootstrap mode (create first baseline).
   - If docs already exist: refresh mode (re-check codebase evidence and update docs in place).
3. Define coverage targets first:
   - design/spec topics (what and why)
   - high-impact modules
   - external knowledge/reference topics
   - required contracts and runtime flows
   - `docs/superpowers/plans/*.md` and `docs/superpowers/specs/*.md` context when present
4. Run split-expansion audit before writing or refreshing docs:
   - Enumerate independent components in each module domain.
   - Enumerate independent subdomains in each design domain.
   - Mark where depth is currently coupled in one file.
5. Build split map before drafting:
   - Enumerate independent components in each target module.
   - Map each component and subdomain to owning code files and target docs path.
   - Preserve all existing detail by relocating to specific docs paths.
   - If one file still owns deep details for 2+ independent concerns, split before drafting.
6. Run architecture reconnaissance before any subagent dispatch:
   - Read code/tests/config/scripts to identify real module boundaries.
   - Read `docs/superpowers/plans/*.md` and `docs/superpowers/specs/*.md` when present.
   - Build a partition map: each partition has source files, target docs paths, and owner.
   - If boundaries are unclear or overlapping, do not dispatch subagents.
7. Lock minimum output quotas before drafting:
   - At least 1 `docs/design/*.md`
   - At least 1 `docs/modules/*.md`
   - `docs/knowledge/*.md` is optional and only allowed when external references are actually used
8. Create required docs structure:
   - docs/architecture.md
   - docs/design/<topic>.md
   - docs/modules/<module>.md
   - docs/knowledge/<topic>.md
9. Enforce canonical path naming before writing content:
   - Use `docs/modules/` (plural) only.
   - Never create legacy singular module-path variants.
   - Use `docs/knowledge/` (singular) only.
   - Never create legacy plural knowledge-path variants.
   - If old/new variants coexist, consolidate to canonical paths and remove non-canonical usage.
   - Author documentation content in English; allow non-English only when quoting source material.
10. Choose execution strategy:
   - Small scope: write docs in the current session.
   - Medium-to-large scope with clear partitions: dispatch subagents for design/modules first, then synthesize architecture in main session.
   - If medium-to-large scope has unclear boundaries: do not dispatch yet; clarify boundaries first.
11. Integrate outputs into a single consistent docs set in the current session.
12. Verify completeness, cross-file consistency, canonical path usage, split-expansion rules, and detail-preservation rules before proceeding.

## Quick Reference
| Step | Action | Output |
|---|---|---|
| Scope confirm | Lock what to document first | Clear module/topic scope |
| Coverage plan | Define module/topic partitions | Explicit docs coverage map |
| Evidence gather | Read code/tests/config/scripts | Source-file-backed facts |
| Plan/spec gather | Read `docs/superpowers/plans/*.md` and `docs/superpowers/specs/*.md` when present | Prior intent and implementation context captured |
| Split audit | Map each component/subdomain to code ownership and doc target | Split-ready expansion plan |
| Mode select | Bootstrap when docs missing; refresh when docs already exist | Correct workflow branch |
| Structure create | Add required docs files | Baseline docs tree exists |
| Detail guard | Split dense docs into multiple module/design docs while preserving depth | No detail loss |
| Parallel drafting | Mandatory for medium-to-large scope with clear partitions; skip when boundaries are unclear | Faster drafts with cleaner main context and controlled risk |
| Integration and QA | Merge + verify links/terminology/contracts | Complete and internally coherent docs |

## Plan/Spec Relevance Rule
When `docs/superpowers/plans/` or `docs/superpowers/specs/` exists, "relevant" files mean:
1. Files whose filename or headings match the current module/topic keywords.
2. Files explicitly linked from related architecture/design/module docs.
3. If no direct match is found, read at least the most recently updated file in each existing directory as minimum context.

## Split-Expansion Rules
Use this to scale detail by splitting, not trimming:

1. A single module domain can use multiple module docs:
   - `docs/modules/<module>.md` for top-level map and shared contracts
   - `docs/modules/<module>-<component>.md` for each deep component
2. A single design topic can use multiple design docs:
   - `docs/design/<topic>.md` for top-level decisions and links
   - `docs/design/<topic>-<subdomain>.md` for each deep subdomain
3. If details increase, expand to more files with explicit links. Never reduce depth to keep one file short.
4. "Same module" or "same design topic" is not a valid reason to keep deep details coupled in one file.

## Implementation
Use this checklist to generate full docs (no placeholders):

```text
1) Confirm docs-init scope and priority modules/topics.
2) Select mode explicitly:
   - bootstrap mode if docs/architecture.md missing
   - refresh mode if docs already exist (must re-check codebase before editing docs)
3) Build a coverage map before writing:
   - design docs for feature intent, tradeoffs, and constraints (what/why); minimum count: 1
   - module docs for high-impact modules; minimum count: 1
   - 1 architecture doc (synthesized after design/modules drafts)
   - knowledge docs for external references and operational notes; optional, only when reference sources exist
4) Define split boundaries up front:
   - list module-level shared sections for `docs/modules/<module>.md`
   - list component-level deep sections for `docs/modules/<module>-<component>.md`
   - list design-level shared sections for `docs/design/<topic>.md`
   - list subdomain-level deep sections for `docs/design/<topic>-<subdomain>.md`
5) Build component boundary map before dispatch:
   - list each concern name, owning code files, and target docs path
   - include migration map: old section -> new target file section
   - confirm no deep section is dropped without relocation
6) Build and validate partition map before dispatch:
   - each partition maps to explicit source files and explicit target docs paths
   - no file overlap between partitions
   - each partition has enough evidence to write concrete docs
   - if any partition is ambiguous, skip dispatch and write in main session
7) Lock canonical directories:
   - docs/architecture.md
   - docs/design/<topic>.md
   - docs/modules/<module>.md (plural directory only)
   - docs/knowledge/<topic>.md (singular directory only)
   - forbid all non-canonical legacy path variants
8) Gather evidence from code/tests/config/scripts.
   - If `docs/superpowers/plans/` exists, read relevant plan docs.
   - If `docs/superpowers/specs/` exists, read relevant spec docs.
9) For refresh mode, perform mismatch scan before writing:
   - map current code paths to existing docs sections
   - mark stale, missing, and structurally outdated areas
   - mark coupled module/design sections that require split expansion
   - update in place before creating any new doc files
10) Draft docs with concrete details (no TBD/TODO/"...").
11) Enforce split-expansion during drafting:
   - if a module doc still contains deep detail for multiple components, split into multiple module docs
   - if a design doc still contains deep detail for multiple subdomains, split into multiple design docs
   - preserve and deepen details in new files; do not collapse to summaries
12) Enforce detail-preservation during drafting:
   - every moved section must appear in target docs with equivalent or richer detail
   - include explicit cross-links from parent doc to split docs
13) For medium-to-large scope with clear partitions, dispatch subagents in parallel to draft design/modules.
14) Integrate subagent outputs in current session and enforce consistent terminology.
15) Synthesize docs/architecture.md in current session from consolidated design/modules outputs.
16) Validate paths and resolve conflicts:
   - if legacy module-path variants coexist with canonical module paths, merge into canonical module paths
   - if legacy knowledge-path variants coexist with canonical knowledge paths, merge into canonical knowledge paths
   - update references that point to any legacy path variants
17) Enforce output-balance, workflow-order, split-expansion, and detail-preservation gates (hard fail if violated):
   - fail if no docs/design/*.md generated
   - fail if no docs/modules/*.md generated
   - fail if refresh mode skipped codebase mismatch scan and edited docs blindly
   - fail if component boundary map was not produced before refresh edits
   - fail if refreshed module docs keep deep detail for multiple independent components in one file
   - fail if refreshed design docs keep deep detail for multiple independent subdomains in one file
   - fail if split was done by deleting detail instead of relocating and linking
   - fail if medium-to-large scope skips subagent draft phase despite clear partitions
   - fail if architecture synthesis happens before design/modules evidence exists
   - fail if growth pressure is handled by summary-only compression instead of split expansion
   - fail if `docs/superpowers/plans/` exists and relevant plan docs were not reviewed
   - fail if `docs/superpowers/specs/` exists and relevant spec docs were not reviewed
   - fail if generated docs contain non-English narrative text (except quoted source material)
   - fail if knowledge docs are generated without external sources
   - fail if any knowledge doc lacks related design/modules links
   - fail if knowledge docs outnumber design docs unless user explicitly requested reference-heavy bootstrap
18) Verify every major claim has source-file evidence and assumptions are explicit.
19) Produce a completeness report (coverage, unknowns, follow-ups), including component split map.
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
Draft docs for this partition according to active mode:
- Bootstrap mode: create first-version docs.
- Refresh mode: re-check codebase evidence and update existing docs in place.

Hard constraints:
1) Do NOT edit docs/architecture.md.
2) Do NOT edit docs outside the allowed target paths.
3) Every major claim must be backed by source-file evidence.
4) No placeholders (TBD/TODO/"...").
5) Write all documentation narrative in English.
6) Keep terminology consistent with existing docs.
7) If evidence is insufficient, state explicit assumptions and unknowns.
8) Keep module docs contract-focused; place long feature walkthroughs and validation matrices in design docs and link them.
9) In refresh mode, do not recreate stable docs from scratch; patch stale sections first.
10) If scope includes multiple independent concerns, return a split map and expand into separate module/design docs without detail loss.
11) If `docs/superpowers/plans/` or `docs/superpowers/specs/` exists for this partition, read and cite relevant files as context evidence.

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
- Under authority/time pressure, agents rationalized adding everything into one module file for "self-contained review", creating hard-to-maintain mega-files.
- When docs already existed, agents rationalized routing to check-only sync gate and skipped codebase-wide recheck + docs updates requested by user.
- In refresh mode, agents sometimes patched changed lines but left independent component details coupled in one module doc.
- Agents sometimes interpreted "concise" as "delete details," causing information loss during split.

## Rationalizations And Counters
| Excuse | Reality |
|---|---|
| "I can skip docs for now" | Missing docs blocks safe project-context decisions. |
| "I should use sync skill for init" | Sync skill assumes docs already exist; initialize first. |
| "One giant pass is always simpler" | Large scope benefits from partitioned parallel drafts and main-session integration. |
| "Parallel subagents will always conflict" | Conflicts are avoidable with strict partition boundaries and file ownership. |
| "Leadership needs architecture in 20 minutes, write it first from assumptions" | Architecture-first without design/modules evidence creates drift and rework; produce evidence-backed drafts first, then synthesize architecture. |
| "Reviewers want one file, so module docs must include everything" | Keep module docs as concise contract/index docs; satisfy one-file navigation with links, not duplicated deep detail. |
| "Docs already exist, so run sync check only" | If user asked docs re-initialization/refresh, this skill must re-check the codebase and update docs in place. |
| "Only one or two sections changed, skip mismatch mapping" | Refresh mode still requires a codebase mismatch scan before edits; small scope changes are where hidden drift is easiest to miss. |
| "These are all in the same module, so keep one detailed doc" | Same module can still contain multiple independent components; keep module summary unified but split deep component details into linked docs. |
| "To split faster, trim low-priority details" | Split means redistribute and preserve depth; do not reduce detail to force brevity. |
| "Design can stay monolithic; only module needs split" | Design subdomains also split when deep concerns diverge; growth applies to both module and design docs. |

## Red Flags - Stop And Initialize
- "Let's code first, docs later"
- "We only need architecture, skip other docs"
- "Write architecture first and backfill design/modules later"
- "I'll add placeholders and fill later"
- "One agent should do everything in one giant context"
- "Let's keep all flows, tests, and checklists in one module file for convenience"
- "Since docs exist, we should skip re-audit and just run the completion gate"
- "It is only two sections, edit directly without mismatch mapping"
- "They are in one module anyway, no need to split components"
- "Let's shorten docs while splitting so it is easier"
- "Only modules need split, design can stay one giant file"

Any red flag means complete first-version docs before proceeding.

## Common Mistakes
- Creating only docs/architecture.md and skipping design/modules/knowledge docs.
- Writing docs/architecture.md first from assumptions in medium-to-large scope.
- Dispatching subagents before understanding real module boundaries.
- Generating many knowledge docs while producing zero design docs.
- Treating knowledge docs as mandatory output instead of reference-conditional output.
- Mixing canonical paths with legacy singular/plural variants.
- Treating module docs as catch-all dumps for volatile feature details.
- Treating docs-exist cases as check-only operations instead of rechecking codebase evidence and updating docs.
- Refreshing content without producing component boundary map and component split outputs.
- Skipping `docs/superpowers/plans/*.md` or `docs/superpowers/specs/*.md` context when those paths exist.
- Splitting by deleting details instead of relocating details with links.
- Splitting module docs while leaving design docs monolithic under the same growth pressure.
- Writing generic docs without concrete source-file evidence.
- Leaving placeholders (TBD/TODO/...) in final docs.
- Dispatching subagents without independent partitions, causing overlap and churn.
- Using this skill for post-implementation sync updates.
