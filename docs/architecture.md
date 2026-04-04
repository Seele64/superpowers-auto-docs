# Architecture

## Purpose
This document defines system-level architecture, boundaries, and major technical decisions for the superpowers skills repository.

## Scope
- Repository-level source-of-truth layout
- Distribution and sync flow to local runtime environments
- Cross-cutting operational constraints for safe synchronization

## Non-Scope
- Feature-level design details (use `docs/design/`)
- Module internals and implementation walkthroughs (use `docs/modules/`)
- Third-party references and notes (use `docs/knowledge/`)

## Repository Purpose
This repository maintains reusable superpowers skills and top-level agent instructions.

## Source of Truth
- `skills/`
- `AGENTS.md`

## Distribution Flow
1. Source of truth lives in the repository root.
2. Local runtime consumes mirrored files from the opencode config path:
	- `~/.config/opencode/skills/`
	- `~/.config/opencode/AGENTS.md`
3. Sync operation is handled by `sync-opencode.sh`.

## Operational Constraint
The sync script must be safe to run repeatedly and keep target files aligned with repository state.
