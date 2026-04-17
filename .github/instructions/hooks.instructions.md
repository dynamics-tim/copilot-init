---
applyTo: "references/hooks/**/*.json, .github/hooks/**/*.json"
---

<!-- managed-by: copilot-init -->

# Hook Configuration Conventions

## Structure

- Every hook config file must have `"version": 1` at the top level.
- Use the `hooks` object with event names as keys: `sessionStart`, `sessionEnd`, `postToolUse`, `preToolUse`, `userPromptSubmitted`, `errorOccurred`.
- Each event contains an array of hook objects with `event`, `steps` fields.

## Dual-Platform Commands

- Every `command` step must include both `bash` and `powershell` fields.
- Both must produce identical behavior — test on both platforms.
- Bash: use `|| true` for non-critical commands to avoid breaking the hook chain.
- PowerShell: wrap non-critical commands in `try {} catch {}`.

## Performance

- Hooks fire on every tool call — keep them under 100ms execution time.
- Prefer simple append operations over read-modify-write.
- Use `timeoutSec: 5` as default; hooks that exceed timeout are killed.

## Conventions

- Use JSONL (one JSON object per line) for log files — append-only, no race conditions.
- Store ephemeral data in `.copilot/` (add to `.gitignore`).
- Use `$COPILOT_TOOL_NAME` and `$COPILOT_TOOL_ARGS` env vars in postToolUse hooks.
- Add `_comment` field for human-readable documentation inside JSON configs.

<!-- end-managed-by: copilot-init -->
