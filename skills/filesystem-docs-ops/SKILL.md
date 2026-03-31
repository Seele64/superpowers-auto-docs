---
name: filesystem-docs-ops
description: Use when creating or updating project docs where weak evidence, stale claims, or wrong file targeting could corrupt documentation quality.
---

# Filesystem Docs Operations

## Overview
This skill provides deterministic filesystem operations for docs updates with evidence gates.
Core principle: no docs claim without verifiable evidence.

## Canonical Docs Layout
- docs/architecture.md
- docs/designs/*.md
- docs/knowledge/*.md

## Evidence Contract
Use this triplet for every mismatch fix:
- doc_claim: exact statement currently in docs.
- code_location: file path plus line reference.
- actual_behavior: observed behavior from code or executable evidence.

If any triplet field is missing, do not rewrite the claim. Record it as unresolved.

## Procedure
1. Ensure docs/, docs/designs/, and docs/knowledge/ exist.
2. Inventory docs files and read likely target files before writing.
3. Route content:
   - architecture/system behavior -> docs/architecture.md
   - feature/task design -> docs/designs/*.md
   - verified commands/facts -> docs/knowledge/*.md
4. Update existing files first; create new files only when no suitable file exists.
5. Apply auto-update rules:
   - preserve existing useful headings
   - remove duplicate and replaced stale claims
   - merge related updates in place instead of scattering
6. Apply auto-fix rules for mismatches:
   - require complete evidence triplet
   - rewrite only the contradicted statement and nearby dependent text
   - if behavior is unclear, keep verified facts and add an open question
7. Verify writes by re-reading touched files and checking:
   - no placeholder-only section bodies
   - no speculative claims written as facts
   - no contradictions across touched docs

## Quick Reference
- Missing docs structure -> create directories first.
- Mixed updates -> split across architecture/design/knowledge targets.
- Incomplete evidence triplet -> do not claim a fix.
- Ambiguous behavior -> open question, not asserted fact.

## Output Contract
- Created directories (if any).
- Created files (if any).
- Updated files with short rationale per file.
- Mismatch table with resolved and unresolved items.
- Open questions requiring human confirmation.

## Completion Checklist
- Docs directory structure exists.
- All writes are categorized into architecture/design/knowledge targets.
- Existing files were updated before creating new files.
- Documented claims are evidence-backed.
- Unresolved mismatches are explicitly reported.

## Integration
- Used by: superpowers:docs-context-bootstrap
- Used by: superpowers:docs-categorize
- Used by: superpowers:docs-init-from-empty
