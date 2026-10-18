# Claude Code CLI adapter

Check `claude --help` locally. `-p`/`--print` is the non-interactive mode; it
is distinct from an interactive CLI or a built-in Agent tool. Use existing
authentication/model configuration. CLI headless mode can load project hooks
and MCP settings, so inspect the workspace configuration before launching it.

## Editable worker with bounded checks

Prepare an isolated workspace, a self-contained brief and private new log paths.
Set a task-specific budget. Replace the example test commands with the actual
authorized project checks; tool auto-approval patterns are not an OS sandbox.

```sh
: "${DELEGATION_WORKTREE:?Set the worker workspace}"
: "${DELEGATION_BRIEF:?Set the brief file}"
: "${DELEGATION_EVENTS:?Set a new JSONL output file}"
: "${DELEGATION_STDERR:?Set a new stderr log}"
: "${DELEGATION_BUDGET_USD:?Set the permitted CLI budget}"

(
  cd "$DELEGATION_WORKTREE" || exit
  claude --print --permission-mode acceptEdits --permission-prompts none \
    --tools 'Read,Edit,Write,Glob,Grep,Bash' \
    --allowedTools 'Read' 'Edit' 'Write' 'Glob' 'Grep' \
      'Bash(npm run lint)' 'Bash(npm test)' \
    --disallowedTools 'Bash(git push *)' 'Bash(git tag *)' 'Bash(git commit *)' \
    --max-budget-usd "$DELEGATION_BUDGET_USD" \
    --output-format stream-json --verbose \
    < "$DELEGATION_BRIEF" > "$DELEGATION_EVENTS" 2> "$DELEGATION_STDERR"
)
```

Pass lists as explicit quoted arguments; this avoids shell-specific splitting
of a space-delimited variable. `--permission-prompts none` denies operations
that would need an unanswered prompt; it does not bypass permission checks.
The tool list selects built-in tools; separately audit any connected MCP tools.
When strict filesystem/process isolation is required, provide it externally.

If the worker has no shell role, remove Bash from `--tools`, explicitly disallow
Bash and assign verification to the coordinator. For a read-only worker use a
read-only isolated workspace and a read-tool list. Do not describe plan mode or
an allowed-tool list alone as an enforced filesystem sandbox.

`stream-json` with `--verbose` emits JSONL. Inspect the final result event and
actual process exit status; a started stream is not completion. The session ID
comes from the recorded output. Use the process runner's background/session
support, not `--bg` combined with print mode.

## Resume

Use `--print --resume "$DELEGATION_SESSION_ID"` in the same workspace, with a
new follow-up brief, new log paths and the required permission/tool/budget flags.
Do not use an ambiguous continue/latest selection in parallel work. Inspect
saved session behavior before changing roles or permissions; use a new session
when the existing one cannot support the required boundary.

Keep subscription/auth behavior intact. Do not add `--bare` as a generic speed
optimization: it changes context/config discovery and authentication behavior.
Choose it only when the user's scripted environment is configured for that mode.

Sources: official [headless mode](https://code.claude.com/docs/en/headless) and
[CLI reference](https://code.claude.com/docs/en/cli-reference). Examples were
checked against local Claude Code 2.1.302 help on 2026-10-18; recheck other versions.
