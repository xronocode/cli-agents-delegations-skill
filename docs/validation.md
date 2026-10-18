# Validation

Checked on 2026-10-18. Version 0.2.0 renames the skill and makes invocation explicit-only.

| Check | Result | Boundary |
| --- | --- | --- |
| YAML metadata, naming and invocation policy | Checked for the new name, Claude's `disable-model-invocation: true` and Codex's `allow_implicit_invocation: false` | Static configuration checks, not a live host invocation |
| Bundled Codex scaffold validator | Version 0.1.0 passed; version 0.2.0 uses Claude's documented `disable-model-invocation` extension, which this validator's field allowlist rejects | The extension is intentional; the strict validator is not reported as passing |
| `npx skills add ./ --list` | Discovers one skill, `cli-agents-delegations-skill` | Validates local package discovery |
| Local Markdown links and private-path scan | Passed | No required private machine paths in the package |
| Shell syntax and mock execution | Five snippets passed in version 0.1.0; recipes unchanged in version 0.2.0 | No model-backed worker was launched |
| Git workspace example | Created a worktree at the captured base in a disposable repository in version 0.1.0; recipe unchanged | No primary project checkout was changed |

The mock runs exercised new/resumed Codex launches, Claude print mode and the
ZCode prompt adapter. They checked argument boundaries, working-directory
selection, stdin or quoted prompt text, paths with spaces and stdout/stderr/result
files. Literal shell payloads in the synthetic brief were preserved as text.
This does not prove a provider's permissions, authentication, output schema or
model response; validate those with a small authorized smoke run on the target host.

Adapter flags were checked against installed help:

- Codex CLI 0.163.0, plus official OpenAI documentation.
- Claude Code 2.1.302, plus official Claude Code documentation.
- ZCode 0.16.9. Operational mode caveats also come from the author's original notes.

Each adapter links its sources and states its limits. Recheck the installed
CLI's help before using the recipe with another version. Models, credentials,
dependencies and local inference engines are not installed or started by this package.
