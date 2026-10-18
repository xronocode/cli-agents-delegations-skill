# Git and runtime handling

Read the parts relevant to the actual host. These are coordination patterns,
not an instruction to install another agent or change permissions.

## Map the headless process contract

| Mechanism | Verify before launch | Completion evidence |
| --- | --- | --- |
| Working directory | CLI flag or process cwd; one task-owned workspace | Recorded cwd and inspected output paths |
| Non-interactive mode | Explicit exec/print/prompt mode; no hidden TUI or permission prompt | Process starts with redirected stdin/stdout and reaches a terminal result |
| Permissions | Real file/tool/sandbox controls and their limits | Effective configuration, tool-denial events and actual changed paths |
| Continuation | Exact CLI session ID, supported resume flags and workspace | The intended session continues; no use of an ambiguous latest session |
| Completion | Process-runner handle, stdout/stderr, result schema and exit code | The original process is terminal and its result/diff is inspected |
| Resource limits | Native budget flags plus an external time/process limit when needed | Actual usage and a bounded stop/recovery path |

Use the selected runtime's current documentation or local help. Do not infer
capabilities from a mode name or copy flags from a different agent/version.
Before a new adapter handles consequential work, run a small authorized task
in isolation and verify its working directory, write behavior, output and
completion signal. Keep help-only validation distinct from a real run. Native
subagents are a different mechanism and do not validate this CLI adapter.

Use the host's process runner to launch without blocking the coordinator. Keep
the returned handle for polling/stopping; store a separate CLI session ID for
resume after the process exits. Never assume that appending an ampersand makes
a job survive the enclosing runner or provides a recoverable exit code. If a
supervisor is needed, select one supported on that host and capture its job ID.

Pass prompts and arguments through structured tool fields, files, or correctly
quoted argument arrays. Do not construct an executable shell string by joining
untrusted task text. Quoted heredocs preserve literal shell characters; they do
not make the later use of that text safe automatically.

## Prepare a Git worker

Use a unique existing repository path, target ref, new branch and non-existing
workspace path. Read applicable instructions before writing. Capture the dirty
state so later inspection can distinguish user changes from task changes.

For a POSIX shell, after setting these task-specific variables deliberately:

```sh
: "${DELEGATION_REPO:?Set the existing repository path}"
: "${DELEGATION_TARGET:?Set the intended target ref}"
: "${DELEGATION_BRANCH:?Set a new task branch name}"
: "${DELEGATION_WORKTREE:?Set a new worktree path}"

git -C "$DELEGATION_REPO" status --short
git -C "$DELEGATION_REPO" check-ref-format --branch "$DELEGATION_BRANCH"
DELEGATION_BASE_SHA=$(git -C "$DELEGATION_REPO" rev-parse --verify "${DELEGATION_TARGET}^{commit}")
git -C "$DELEGATION_REPO" worktree add -b "$DELEGATION_BRANCH" "$DELEGATION_WORKTREE" "$DELEGATION_BASE_SHA"
```

Run preparation fail-fast and inspect each failure; do not continue with an
empty/unresolved base revision. The commands do not switch the primary checkout
or include its uncommitted edits. If the task needs uncommitted source changes,
arrange an explicit sanitized snapshot rather than pretending the base includes them.
On other hosts, use the equivalent structured Git operations.

Store the base SHA, branch and workspace with the task record. Worker branches
should not track a publishing remote by default. Prompt restrictions alone do
not prevent a worker with unrestricted shell access from reaching that remote.

Linked worktrees share the repository's common Git directory. A sandbox that
permits source edits may still deny commits or worktree metadata access. Do not
automatically expand its writable paths: keep Git mutations with the coordinator,
use an independent clone, or collect a patch/artifact instead.

For non-Git work, use a separate task output directory with explicit input
copies/references and preserve the same single-writer ownership rule.

## Dependencies and generated files

Prefer the project's supported per-workspace dependency setup or caches.
Sharing a mutable dependency directory can couple workers and confuse tools
that resolve a symlink's real path. For example, browser/test tools may treat
symlinked dependencies as outside the allowed project root. Diagnose such a
failure before classifying it as a product regression.

The coordinator prepares dependencies when installation is outside worker
permissions. Do not let multiple workers modify one lockfile or dependency
tree concurrently. Assign generated files to one owner and regenerate from the
source changes during integration instead of hand-merging minified output.

## Verify the candidate before integration

Inspect the diff from the recorded base and compare paths with the assigned
scope. Review new/untracked files explicitly; stage only task-owned paths when
a commit is authorized. A blanket add can capture logs, credentials or user files.

For a bug fix, a useful regression check uses a disposable baseline candidate
with the new test but without the fix. Establish that the failure exercises the
bug, rather than a missing fixture, changed API or broken environment. Do not
reset or partially revert an active working copy to manufacture this check.

Before integration, capture the current target and prepare an isolated
integration branch/worktree. Apply the candidate through the project's chosen
merge/patch mechanism, resolve conflicts there, and run required checks against
that exact tree. Tests in a worker's old base do not prove an updated integration
candidate passes. If the target changes before the actual integration, repeat
the affected integration checks or use a mechanism that rejects a stale target.

Read-only reviewers may share this frozen candidate. Request findings with
severity, location, why it matters, expected fix and verification target.
The coordinator handles overlapping edits and assesses the actual evidence.

## Finish or recover

Use a compact run record containing:

- Task and worker/session/process IDs; workspace and base revision.
- Current state from the runtime: pending, running, terminal, or unknown.
- Allowed write scope, output/evidence paths, and limits.
- Verified result and whether it is integrated/published.

An observation timeout means the state is unknown until checked again. Inspect
the same handle before retrying; do not start a duplicate writer. Resume only
when the runtime supports the intended continuation and permissions. Start a
new bounded run if the existing session cannot change to the required role.
With parallel CLI runs, never use an unqualified "resume latest" command:
resume the exact session ID recorded for that task and role.

If a worker cannot complete a check, let the coordinator run it with existing
authorization or return the concrete blocker. Do not invent approval from
elapsed time, claim success from a prose report, or weaken the gate to finish.

After an authorized push, observe the required CI for the actual commit. A local
pass and a successful push are separate states. Do not add a push to tasks that
only request a local change or review. Clean up task-owned worktrees only after
their processes are terminal and their result/evidence is retained. Do not force
removal of a dirty worktree or delete unrelated branches.
