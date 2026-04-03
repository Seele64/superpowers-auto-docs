# Knowledge: Opencode Config Sync

## Goal
Keep `skills/` and `AGENTS.md` in this repository synchronized to `~/.config/opencode/`.

## This Knowledge Covers
- Verified behavior of the sync script
- How the repository source-of-truth is mirrored into the local OpenCode config directory
- Notes that were gathered from the repo, official docs, or user-provided facts during initialization

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