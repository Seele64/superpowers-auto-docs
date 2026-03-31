---
name: docs-categorize
description: Use when mixed findings must be split into architecture, design, and knowledge docs without collapsing into shallow one-line updates.
---

# Docs Research Categorizer

## Overview
Classify findings into architecture, design, and knowledge docs with depth checks and contradiction cleanup.

## Required Sub-Skill
- Use superpowers:filesystem-docs-ops before categorizing and writing.

## Classification Rules
1. Architecture updates -> docs/architecture.md:
	- system structure, component interactions, key decisions, constraints, rationale.
2. Design updates -> docs/designs/*.md:
	- feature/task design, goals/non-goals, interfaces, data impact, rollout, risks, validation plan.
3. Knowledge updates -> docs/knowledge/*.md:
	- verified environment facts, commands, workflow constraints, domain terms.
4. Mixed updates must be split across architecture, design, and knowledge docs.

## Depth Gates
Before finalizing any write:
- Architecture entries must include both decision and rationale.
- Design entries must include at least goals, interfaces, and validation notes.
- Knowledge entries must include verifiable source context (command, file, or user-provided fact).

If a finding is too shallow to satisfy the gate, write it as an open question instead of a fact.

## Procedure
1. Invoke superpowers:filesystem-docs-ops to ensure directory structure and inventory existing docs files.
2. Read findings and split content by architecture/design/knowledge.
3. Update docs/architecture.md with structured sections and explicit rationale.
4. Update matching docs/designs/*.md files first; create a new design file only when no relevant topic file exists.
5. Update docs/knowledge/*.md with verified facts only.
6. Run an auto-fix pass via superpowers:filesystem-docs-ops:
	- remove duplicate or stale statements,
	- resolve contradictions between changed docs sections,
	- flag unresolved uncertainties as open questions instead of facts.
7. Enforce depth gates; downgrade shallow claims to open questions.
8. Re-read touched files and confirm consistent categorization.

## Output Contract
- Updated docs/architecture.md entries as needed.
- Updated docs/designs/*.md files for matching topics.
- New docs/designs/*.md files only when required.
- Updated docs/knowledge/*.md files for verified facts.
- Categorization summary and auto-fix changelog.
- Open-question list for deferred facts.

## Completion Checklist
- Content is categorized and not dumped into a single file.
- Existing files were updated before creating new files.
- No speculative claims are written as facts.
- Contradictions and stale sections were handled or explicitly flagged.
- Depth gates were satisfied or converted to open questions.
