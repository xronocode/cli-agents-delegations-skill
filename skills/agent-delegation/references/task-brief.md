# Delegation brief template

Use this as a starting point, not a requirement to add empty sections. Replace
the example fields with the actual task and permitted resources.

```markdown
# Task: short identifier and concrete outcome

## Context
Project and relevant architecture in a few sentences.
Authoritative requirements/specification and applicable repository rules.
Explicit dependencies or interfaces shared with other tasks.

## Result
What must change or be answered.
Acceptance criteria and important edge cases.
For a review: the review scope, severity criteria, and required evidence.

## Workspace and ownership
Workspace: the exact worker-owned path or sandbox.
Base: the captured commit/revision, or a named immutable source snapshot.
CLI role: headless implementation or read-only review; provider/session if fixed.
Allowed writes: specific files/directories or a designated artifact folder.
Excluded areas: the primary checkout, other workers' scopes, user-owned changes.
Shared/generated files: the designated coordinator or owner.

## Permissions and limits
Available tools and enforced runtime permissions.
Unavailable tools, network restrictions, and checks the coordinator must run.
No commit, push, tag, publication, dependency installation, or global input
unless an item is explicitly assigned and authorized.
Do not launch additional agents or switch provider/model unless assigned.
Time/cost/resource limits and when to return a blocker rather than retry.

## Verification
Exact applicable checks and what they demonstrate.
For a regression: the baseline failure and fixed result to establish.
For an artifact: inspect the real output using an appropriate viewer/checker.
Do not disable checks, loosen thresholds, or claim an unrun check passed.

## Final report
Outcome and changed files/artifacts.
Checks actually run, exit/result, and evidence paths.
Checks not run and the concrete reason.
Open questions, remaining risks, and coordinator actions needed.
```

For a read-only investigation, replace write instructions with the expected
answer and sources. When the runtime cannot enforce read-only access, describe
the actual restriction accurately and use a stable copy if needed.

For a worker without shell access, state: "Shell is unavailable. Edit only the
allowed files. Report the checks to run; do not claim they passed or attempt to
obtain another execution channel."
