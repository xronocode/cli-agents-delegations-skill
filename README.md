# cli-agents-delegations-skill

Run separate CLI agents in non-interactive mode, keep working while they run,
and verify what they produced before integrating it. Provider-neutral instructions
for **headless processes**, isolated worktrees, resumable sessions and captured evidence.
Delegation is opt-in: use it only when the user explicitly requests headless CLI agents.

## Install

```sh
npx skills add xronocode/cli-agents-delegations-skill
```

Install globally for a selected supported agent, for example:

```sh
npx skills add xronocode/cli-agents-delegations-skill --skill cli-agents-delegations-skill --global --agent codex --yes
```

List the available skill without installing:

```sh
npx skills add xronocode/cli-agents-delegations-skill --list
```

This repository uses the [Agent Skills layout](https://agentskills.io/specification)
with host-specific invocation settings and the open [skills CLI](https://github.com/vercel-labs/skills).
It needs no npm package publication: the installer discovers `skills/cli-agents-delegations-skill/SKILL.md`
and copies the skill's supporting references.

## Use

```text
$cli-agents-delegations-skill Run these independent tasks through headless CLI agents.
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

### Explicit invocation only

- **Codex:** `$cli-agents-delegations-skill` followed by the task.
  `agents/openai.yaml` sets `policy.allow_implicit_invocation: false`.
- **Claude Code:** `/cli-agents-delegations-skill` followed by the task.
  `SKILL.md` sets `disable-model-invocation: true`.
- **Other hosts:** request headless CLI agents explicitly. The core instructions
  require that request; automatic-loading controls depend on the host's support.

Generic requests such as "parallelize this", "review from four angles" or
"work autonomously" do not authorize headless CLI delegation. Mentioning or
maintaining the skill itself does not authorize a worker run. Authorization
stays with the requested task and its necessary follow-ups.

The invocation settings follow the official [Codex skill documentation](https://learn.chatgpt.com/docs/build-skills)
and [Claude Code skill documentation](https://code.claude.com/docs/en/skills#control-who-invokes-a-skill).

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

- [SKILL.md](skills/cli-agents-delegations-skill/SKILL.md) — core workflow and authority boundaries.
- [Codex invocation policy](skills/cli-agents-delegations-skill/agents/openai.yaml) — explicit invocation only.
- [Task brief](skills/cli-agents-delegations-skill/references/task-brief.md) — adaptable worker brief.
- [Execution reference](skills/cli-agents-delegations-skill/references/execution.md) — runtime selection, Git isolation, verification and recovery.
- CLI adapters: [Codex](skills/cli-agents-delegations-skill/references/codex.md), [Claude Code](skills/cli-agents-delegations-skill/references/claude.md), [ZCode](skills/cli-agents-delegations-skill/references/zcode.md).

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
