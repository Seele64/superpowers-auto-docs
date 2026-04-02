# Module: sync-opencode.sh

## Location
- `sync-opencode.sh`

## Inputs
- Optional argument 1: target directory (default: `$HOME/.config/opencode`).

## Outputs
- Synchronized `skills/` directory under target.
- Synchronized `AGENTS.md` file under target.

## Error Handling
- Exits with non-zero code if source `skills/` or `AGENTS.md` is missing.
- Creates target directory if needed.

## Verification
Run:

```bash
./sync-opencode.sh /tmp/opencode-sync-test
```

Expect:
- `/tmp/opencode-sync-test/skills/` exists.
- `/tmp/opencode-sync-test/AGENTS.md` exists.