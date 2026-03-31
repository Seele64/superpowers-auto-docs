---
name: docs-context-bootstrap
description: Use when implementation work needs code-and-doc context and you must detect and reconcile documentation drift before coding.
---

# Docs Context Bootstrap

## Overview
Build a task-ready context pack from docs plus source evidence, then reconcile mismatches with explicit evidence.

## Required Sub-Skill
- Use superpowers:filesystem-docs-ops only when a docs write or docs repair is actually needed.

## Evidence Schema
Every mismatch entry must include:
- doc_claim
- code_location
- actual_behavior
- status: resolved | unresolved

## Procedure
1. Extract task goal and search keywords from the user request.
2. Perform read-only filesystem inventory:
	- ensure docs/ structure exists
	- inventory current docs files
	- identify likely target files for repairs
	- do not run auto-fix in this step
3. Call the `Explore` subagent with those keywords and require it to:
	- read docs/architecture.md when present
	- read relevant docs/designs/*.md and docs/knowledge/*.md
	- read task-relevant source files
	- return mismatch entries using the evidence schema
4. Validate Explore output:
	- if mismatch entries miss required fields, re-dispatch Explore once with strict schema reminder
	- if still invalid, mark as blocked and ask the human for direction instead of guessing
5. Review top-ranked findings using explicit ranking:
	- business impact
	- proximity to current task
	- confidence of evidence
6. For each mismatch or stale section:
	- verify evidence
	- run superpowers:filesystem-docs-ops only after evidence is validated, to update the correct docs target
	- if evidence is incomplete, mark unresolved and do not rewrite as fact
	- re-run `Explore` for touched docs and related code to confirm alignment
7. Produce a concise context pack for implementation.

## Output
- Short codebase snapshot: purpose, key components, and task-relevant flow.
- Short list of related docs with relevance notes.
- Short list of related source files with implementation observations.
- Short list of risks, assumptions, and open questions.
- Mismatch table: resolved vs unresolved with evidence.
- Docs auto-fix changelog: files updated and why.

## Completion Checklist
- Context includes both docs and source evidence.
- Every mismatch entry follows the evidence schema.
- Docs fixes are written to canonical docs targets.
- Post-fix validation was run on touched areas.
- Unresolved mismatches are called out explicitly.
- Read-only bootstrap happened before any docs write operations.
