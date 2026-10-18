---
name: agent-delegation
description: >-
  Delegate tasks to separate headless CLI agent processes with isolated
  workspaces, self-contained briefs, resumable sessions, captured logs and
  verified integration. Use when CLI-based delegation or parallel CLI workers
  are requested or already authorized. Provider-neutral; not a workflow for
  built-in subagents or GUI automation.
license: MIT
metadata:
  author: xronocode
  version: "0.1.0"
---

# Headless CLI Agent Delegation

Launch separate non-interactive CLI agent processes, observe them, and verify
their candidates before integration. Keep responsibility for the final result.
The CLI provider, model, operating system and project framework are configurable;
the headless-process mechanism is the point of this skill. Do not silently
replace it with built-in subagents or an interactive terminal/GUI session.
This skill does not grant permissions or override restrictions on delegation.

## Decide what to delegate

Read the task and applicable project rules. Preserve the user's scope and
existing authorization. Do not add a confirmation for each ordinary step
inside authorized work; ask only for a missing decision or permission that
actually blocks the next action.

Delegate work that has a useful independent outcome: a bounded implementation,
investigation, review, or artifact. Keep tightly coupled changes together.
Assign shared contracts and generated outputs to a single owner. If ownership
overlaps, change the task boundaries or run the tasks sequentially.

Choose concurrency from the available workers and actual resource limits,
including memory, GPU/inference capacity, provider quotas, and user budgets.
Do not invent a cheaper model, provider, or paid service the user did not select.
If the requested CLI is unavailable or prohibited, report that concrete gap
and continue independent authorized work. Do not claim that a direct answer
or native subagent completed the requested headless run.

## Select and launch a headless CLI

Use the selected provider's installed CLI and existing authentication/model
configuration. Inspect its current help before constructing a launch command.
Read only the adapter relevant to this run:

- [Codex CLI](references/codex.md): non-interactive exec, sandbox, JSONL and explicit session resume.
- [Claude Code CLI](references/claude.md): print mode, tool controls, streaming output and budget.
- [ZCode CLI](references/zcode.md): explicit edit mode, no-shell workers and packaging caveats.
- For another CLI, map working directory, headless mode, permissions, prompt
  input, output, session resume and limits using its actual help/documentation.

Launch through a process runner with background/session support so the
coordinator can continue working and later inspect the same process. Record
that runner's handle and the CLI's session ID separately. Headless means
non-interactive, not automatically detached or persistent after the launcher
exits; use a supported supervisor if that lifetime is required.

Capture stdout events/result, stderr and the eventual exit status under a
unique run ID. Never reuse log paths for a concurrent task or retry. Keep
prompts in a file or stdin when supported; preserve literal shell characters.
Do not change login, install another engine or start local inference implicitly.

## Give each worker a clear boundary

Each writing CLI process gets its own worktree, checkout, sandbox, or output folder
and a bounded write scope. Do not run two writers in the same writable working
copy. Read-only workers may share a stable source snapshot.

For Git work, capture the base commit and target ref before starting. Preserve
user changes and untracked files. A worktree separates changes but is not a
security sandbox: linked worktrees share Git metadata. Avoid granting a worker
access to shared metadata merely to allow commits it was not assigned to make.

Use the CLI/runtime's supported permissions for the role. A read-only brief is not
an enforced read-only mode. Do not enable a permission bypass to work around
tool denials. If the runtime cannot enforce the needed boundary, choose an
isolated alternative, give a narrower task, or perform the action yourself.

Workers do not push, tag, publish, release, alter the primary working copy, or
use global keyboard/mouse input. Keep commits with the coordinator unless a
worker is explicitly assigned to create them. Project-required exceptions
remain subject to the user's authorization and host rules.

For process/workspace preparation and integration details, read
[Git and runtime handling](references/execution.md) when those operations are needed.

## Send a self-contained brief

Use [the brief template](references/task-brief.md), adapted to the task. Include:

- The desired result and acceptance criteria, with relevant source/spec links.
- The exact workspace and base revision, allowed paths and excluded areas.
- Applicable project instructions, public interfaces, and dependencies on others.
- Available tools and permissions, including tools the worker does not have.
- Required checks, resource/time/cost limits, and the expected final report.

Do not assume the worker inherited the conversation. Provide enough context to
work independently without sending unrelated private conversation or secrets.
Keep further delegation with the coordinator unless explicitly assigned.
If shell, network, or writes are unavailable, say so directly and specify how
to report checks that need the coordinator. Do not ask the worker to find a bypass.

## Observe the actual run

Record the task ID, workspace, base revision, worker/session or process handle,
write scope, and evidence locations. Keep prompts and logs out of commits
unless the project intentionally tracks sanitized records.

Use the handle returned by the launch to observe progress and completion.
A conversation claim, stale log, or state file marked "running" is not proof
that a worker is live. On an observation timeout, inspect or poll the same run;
do not launch a duplicate just because observation ended.

If a worker is stuck, examine its actual state. Correct a brief or dependency,
send a bounded follow-up, or stop that run before replacing it. Continue
independent work while a real dependency is pending. Bound retries and spending
to the task's agreed limits; preserve useful completed work when a run fails.

## Verify and integrate

Treat the worker's output as a candidate. Inspect the actual diff or artifact
and check four angles:

1. **Requirements:** the result satisfies the task and relevant contracts.
2. **Evidence:** checks exercise the promised behavior and their results are real.
3. **Scope:** no unrelated files, secrets, generated clutter, or user changes entered the result.
4. **Regressions:** no stubs, skipped checks, weakened thresholds, or hidden fallbacks conceal failures.

Select verification appropriate to the change. For a regression fix, demonstrate
that the targeted test fails on the captured baseline and passes with the fix
when feasible; use a disposable workspace rather than reverting the user's
working copy. Documentation or cosmetic work does not require invented tests.
Reading code is not a substitute for an unrun test.

Integrate in an isolated candidate based on the current target revision and
run the applicable checks there before changing the target. Reconcile conflicts
and regenerate derived files from their sources. If the target advances or
conflict resolution changes the result, revalidate the affected checks against
the exact candidate to be integrated.

Only the coordinator performs authorized commit/push/publication actions.
Worker authorization does not imply publication authorization. If a push is
part of the user's task, observe the required remote checks and report their
actual state. Remove only task-owned, inactive workspaces after preserving the
result and useful evidence; preserve failed candidates needed for recovery.

Report the delivered result, relevant checks and remaining gaps. Distinguish
implemented, tested, integrated, and published states. The coordinator owns
closing the original task rather than merely collecting worker reports.
