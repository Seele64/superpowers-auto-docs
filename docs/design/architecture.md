# Design

## Repository Purpose
This repository maintains reusable superpowers skills and top-level agent instructions.

## Distribution Flow
1. Source of truth lives in repository root:
   - `skills/`
   - `AGENTS.md`
2. Local runtime consumes mirrored files from:
   - `~/.config/opencode/skills/`
   - `~/.config/opencode/AGENTS.md`
3. Sync operation is handled by `sync-opencode.sh`.

## Operational Constraint
The sync script must be safe to run repeatedly and keep target files aligned with repository state.