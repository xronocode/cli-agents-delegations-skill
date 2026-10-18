# Headless CLI Agent Delegation

Run separate CLI agents in non-interactive mode, keep working while they run,
and verify what they produced before integrating it. Provider-neutral instructions
for **headless processes**, isolated worktrees, resumable sessions and captured evidence.

## Install

```sh
npx skills add xronocode/agent-delegation
```

Install globally for a selected supported agent, for example:

```sh
npx skills add xronocode/agent-delegation --skill agent-delegation --global --agent codex --yes
```

List the available skill without installing:

```sh
npx skills add xronocode/agent-delegation --list
```

This repository uses the [Agent Skills format](https://agentskills.io/specification)
and the open [skills CLI](https://github.com/vercel-labs/skills). It needs no npm
package publication: the installer discovers `skills/agent-delegation/SKILL.md`
and copies the skill's supporting references.

## Use

```text
Use agent-delegation to run these independent tasks through headless CLI agents.
Give each process an isolated workspace and a self-contained brief.
Keep the process handles and session IDs, verify the results, then integrate.
```

```text
Run headless CLI reviewers for requirements, evidence, scope and regressions.
Return concrete findings and verify each worker's source references.
```

The core workflow is independent of a particular CLI provider. Concrete adapters
cover Codex CLI, Claude Code CLI and ZCode CLI; other CLIs can use the same process
contract. Built-in subagents and GUI automation are outside this skill's scope.
The host must provide and authorize the selected CLI; installation does not add
an engine, authentication, model access or permissions.
Automatic discovery and explicit invocation depend on the host's settings.

## What it changes

| Concern | Coordination rule |
| --- | --- |
| Ownership | One writer per isolated working copy; read-only reviews can share a stable snapshot |
| Context | Self-contained briefs with scope, available tools and acceptance criteria |
| Runtime | Check real capabilities instead of assuming vendor-specific flags or mode names |
| Headless execution | Explicit non-interactive launch, a real process handle and a separate CLI session ID |
| Progress | Observe an actual run handle; a stale log is not a live worker |
| Quality | Inspect output and evidence before integration; preserve meaningful regression checks |
| Integration | Validate the exact candidate against the current target before updating it |
| Permissions | Reuse existing authorization; keep publishing with the coordinator and within the user's task |

## Included files

- [SKILL.md](skills/agent-delegation/SKILL.md) — core workflow and authority boundaries.
- [Task brief](skills/agent-delegation/references/task-brief.md) — adaptable worker brief.
- [Execution reference](skills/agent-delegation/references/execution.md) — runtime selection, Git isolation, verification and recovery.
- CLI adapters: [Codex](skills/agent-delegation/references/codex.md), [Claude Code](skills/agent-delegation/references/claude.md), [ZCode](skills/agent-delegation/references/zcode.md).

Adapted from the author's CLI delegation protocol. Machine-specific paths,
project frameworks, fixed model choices and dependency setup assumptions have
been removed from the core workflow. Provider-specific switches stay in optional
adapters and must be checked against the installed CLI. No external worker or
inference engine runs as part of installation.

## Validation

Discovery and installation can be checked with the commands above. Those checks
prove packaging, not every host's delegation behavior. Validate a new runtime
adapter with a small authorized isolated task before using it for consequential work.
See the [validation record](docs/validation.md) for the checks and their limits.

## License

[MIT](LICENSE).
