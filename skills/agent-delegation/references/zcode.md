# ZCode CLI adapter

Use an already installed ZCode CLI entry and inspect its `--help`. Some releases
bundle a Node CLI with a desktop application rather than installing a command
on PATH. `DELEGATION_ZCODE_ENTRY` identifies that existing `.cjs` entry; it is
not a fixed machine path or permission to install/copy an application.

If preparing a standalone copy is explicitly needed, preserve the package's
companion `provider/` configuration layout. Copying the CLI file alone can
break provider discovery. Do not copy authentication credentials into the task
workspace or publish companion configuration without inspecting it.

## No-shell editable worker

For the checked release, prompt mode defaults to `yolo`. Select `edit`
explicitly and remove Bash explicitly rather than relying on repeated tool
denials. The coordinator runs lint/tests after inspecting the returned diff.

```sh
: "${DELEGATION_ZCODE_ENTRY:?Set the existing ZCode Node entry}"
: "${DELEGATION_WORKTREE:?Set the worker workspace}"
: "${DELEGATION_BRIEF:?Set the brief file}"
: "${DELEGATION_EVENTS:?Set a new JSON output file}"
: "${DELEGATION_STDERR:?Set a new stderr log}"

node "$DELEGATION_ZCODE_ENTRY" -p "$(cat "$DELEGATION_BRIEF")" \
  --cwd "$DELEGATION_WORKTREE" --mode edit --disallowed-tools Bash \
  --json > "$DELEGATION_EVENTS" 2> "$DELEGATION_STDERR"
```

The double-quoted prompt expansion preserves spaces and shell characters;
never pass the resulting prompt through `eval`. Keep the brief non-secret and
bounded because this adapter supplies it as a process argument. Use a supported
file-input option only after verifying its actual semantics in the installed CLI.

The brief must say that shell is unavailable and ask for unrun checks in the
final report. In the checked release, `build` does not provide the editing role
needed here; do not substitute it merely because the name sounds appropriate.
Do not switch to `guarded` or `yolo` to resolve a denial: they do not preserve
this worker's intended boundary. The tool-removal flag removes whole tools;
patterns such as `Bash(git *)` do not create a command-level allowlist.

Use the coordinator's process runner to run in the background, retain its
handle, and inspect terminal status plus returned JSON/diff. `--json` is not a
promise that every release emits the same schema or streams progress events.

## Resume and limits

Resume the exact recorded `sess_...` ID with `--resume`, a follow-up prompt,
the same task workspace and new log paths. Keep the edit/no-Bash boundary.
Some releases fix a session's mode at creation: verify this before continuing
with a different role; create a new bounded session when needed.

The checked help exposes no cost-cap flag. Bound work through the coordinator's
time/process/resource controls and a small brief; do not invent a budget option.
Respect host constraints before starting any CLI backed by local inference.

Source: installed `zcode 0.16.9 --help`, inspected on 2026-10-18, plus the author's
original operational notes. Mode behavior beyond help came from those notes;
adapter examples were not model-backed smoke-tested for this publication.
