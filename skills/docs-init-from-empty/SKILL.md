---
name: docs-init-from-empty
description: Use when docs are absent and available code evidence is sparse, requiring safe initialization that separates verified facts from unknowns.
---

# Docs Init From Empty

## Overview
Initialize a docs baseline from empty state without inventing architecture facts.

## Required Sub-Skill
- Use superpowers:filesystem-docs-ops before and after generation.

## Detail Standard
- No placeholder-only sections.
- No TODO/TBD-only final content.
- Every required section contains concrete project evidence or explicit open questions.
- Assumptions must be clearly labeled and never presented as facts.

## Sparse-Evidence Mode
If code evidence is weak:
- prefer minimal factual statements from observable files
- keep unknown behavior in an Open Questions subsection
- avoid claiming architecture decisions that are not evidenced

## Required Sections
docs/architecture.md:
- System Overview
- Main Components and Responsibilities
- Data and Control Flow
- Key Architectural Decisions and Rationale
- Constraints, Risks, and Open Questions

docs/designs/overview.md:
- Scope and Current Feature Areas
- Design Principles and Non-Goals
- Cross-Cutting Concerns (security, performance, reliability, observability)
- Planned Milestones
- Known Risks and Follow-Up Tasks

docs/knowledge/project-facts.md:
- Environment and Tooling Facts
- Build/Test/Run Commands
- Repo Workflow Constraints
- Glossary and Domain Terms

## Procedure
1. Invoke superpowers:filesystem-docs-ops to ensure docs/, docs/designs/, and docs/knowledge/ exist.
2. Run a codebase scan for concrete system evidence.
3. Create docs/architecture.md if missing using all required architecture sections.
4. Create docs/designs/overview.md if docs/designs/ has no files.
5. Create docs/knowledge/project-facts.md if docs/knowledge/ has no files.
6. Run a post-generation auto-fix pass via superpowers:filesystem-docs-ops:
	- remove placeholders,
	- correct inconsistent statements,
	- flag unknowns as open questions.
7. Run sparse-evidence validation:
	- convert speculative statements to explicit unknowns
	- ensure each asserted fact is traceable to evidence
8. Re-read created files and return summary.

## Output Contract
- docs/architecture.md (created if missing)
- At least one file in docs/designs/
- At least one file in docs/knowledge/
- Section-complete content with concrete details
- Summary of created files and post-generation fixes
- Open questions that require human confirmation

## Completion Checklist
- Missing docs directories are created.
- Architecture, design, and knowledge docs exist after run.
- Generated docs are actionable and evidence-backed.
- Consistency pass completed and documented.
- Sparse-evidence validation completed.
