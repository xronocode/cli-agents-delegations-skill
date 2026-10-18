# Codex CLI adapter

Check `codex exec --help` and `codex exec resume --help` locally. Use existing
authentication and the user's configured model; the examples select no model.
Prepare a task-owned worktree and existing private run directory first.

## New headless run

Set these paths for one unique run. Result/log files must be outside the
worker's committable tree and writable by the CLI's own output handling.

```sh
: "${DELEGATION_WORKTREE:?Set the worker workspace}"
: "${DELEGATION_BRIEF:?Set the brief file}"
: "${DELEGATION_EVENTS:?Set a new JSONL output file}"
: "${DELEGATION_STDERR:?Set a new stderr log}"
: "${DELEGATION_RESULT:?Set a new final-response file}"

codex exec -C "$DELEGATION_WORKTREE" --sandbox workspace-write \
  --json --output-last-message "$DELEGATION_RESULT" - \
  < "$DELEGATION_BRIEF" > "$DELEGATION_EVENTS" 2> "$DELEGATION_STDERR"
```

Launch this through the coordinator's background/session process runner. Retain
its handle and final exit status. `--json` writes JSONL events; the final-message
file is separate. For a read-only review, select `--sandbox read-only` instead.
Check actual host policies; do not assume network or arbitrary directories are writable.

Do not add the repository's common Git directory to `--add-dir` just to let a
worker commit. Commits remain with the coordinator unless assigned. Never add
approval/sandbox bypass flags to solve a denied command; return that operation
to the coordinator or change the isolation strategy within existing authorization.

## Continue the exact session

After the original process reaches a terminal state, obtain its exact session
ID from the recorded events. Check resume help; it does not necessarily accept
all new-run flags. Verify the saved workspace/permissions fit the continuation.

```sh
: "${DELEGATION_WORKTREE:?Set the same worker workspace}"
: "${DELEGATION_SESSION_ID:?Set the exact task session ID}"
: "${DELEGATION_FOLLOWUP:?Set the follow-up brief file}"
: "${DELEGATION_EVENTS:?Set a new JSONL output file}"
: "${DELEGATION_STDERR:?Set a new stderr log}"
: "${DELEGATION_RESULT:?Set a new final-response file}"

(
  cd "$DELEGATION_WORKTREE" || exit
  codex exec resume "$DELEGATION_SESSION_ID" --json \
    --output-last-message "$DELEGATION_RESULT" - \
    < "$DELEGATION_FOLLOWUP" > "$DELEGATION_EVENTS" 2> "$DELEGATION_STDERR"
)
```

Avoid `--last` when tasks run concurrently. A session ID and a live process
handle are different things: a saved session is not evidence the process runs.

Sources: official [non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode)
and [CLI reference](https://learn.chatgpt.com/docs/cli/reference). Examples were
checked against local `codex-cli 0.163.0` help on 2026-10-18; recheck other versions.
