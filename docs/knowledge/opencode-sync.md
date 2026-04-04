gao# Opencode Config Sync Knowledge

## Goal
Keep `skills/` and `AGENTS.md` in this repository synchronized to `~/.config/opencode/`.

## Script Entry
- `sync-opencode.sh`

## Behavior
- Uses repository path of the script as source root.
- Syncs `skills/` with mirror semantics.
- Copies `AGENTS.md` to target root.
- Accepts optional custom target directory as first argument.

## Notes
- Preferred engine is `rsync -a --delete`.
- Falls back to `cp -a` when `rsync` is unavailable.
