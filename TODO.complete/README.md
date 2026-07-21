# TODO.complete — Work tracker for the kimi session continuation

This directory holds the granular TODOs that complete the work paused
in the kimi session export (`kimi-export-session_-20260721-150805.md`).
Each file is self-contained: priority, scope, acceptance criteria.

## Active work (execute in order)

| #                                                                | P   | Repo                                  | Description                                                           |
| ---------------------------------------------------------------- | --- | ------------------------------------- | --------------------------------------------------------------------- |
| [01](01-fix-mn-document-specs.md)                                | P0  | `metanorma/metanorma-document`        | Fix the 6 failing specs (TermNum heading markup).                    |
| [02](02-audit-mn-document-quality.md)                            | P0  | `metanorma/metanorma-document`        | Audit dirty WIP for quality standards.                                |
| [03](03-commit-and-pr-mn-document.md)                            | P0  | `metanorma/metanorma-document`        | Commit dirty WIP + rebase on origin/main (which has 0.5.0).          |
| [04](04-audit-mn-oiml-quality.md)                                | P0  | `metanorma/metanorma-oiml`            | Audit dirty WIP for quality standards.                                |
| [05](05-commit-and-pr-mn-oiml.md)                                | P0  | `metanorma/metanorma-oiml`            | Commit dirty WIP + 5 unpushed commits.                                |
| [06](06-update-specs-workflow.md)                                | P0  | `oimlsmart/specs`                     | Update `.github/workflows/generate.yml` for the renamed class.        |
| [07](07-local-e2e-verification.md)                               | P0  | all                                   | Local end-to-end run; confirm parity 100%.                            |
| [08](08-verify-gha-deployment.md)                                | P1  | `oimlsmart/specs`                     | Verify GHA deployment is green and site is correct.                   |

## Backlog (not required for current initiative)

| #                                                                | P   | Repo                                  | Description                                                           |
| ---------------------------------------------------------------- | --- | ------------------------------------- | --------------------------------------------------------------------- |
| [99](99-oiml-existing-quality-debt.md)                           | P2  | `metanorma/metanorma-oiml`            | Pre-existing `require_relative`, `instance_variable_get`, `respond_to?` violations. |

## Dependency chain

```
01 ─┐
    ├─► 03 ──────────────────────────┐
02 ─┘                                │
                                     │
04 ─► 05 ──────────────────────────► │ ─► 07 ─► 08
                                     │
                      06 ────────────┘
```

TODO-03, TODO-05, TODO-06 must all merge before TODO-07 can verify
cleanly, and before TODO-08 sees a green CI on `main`.

## Global rules in effect

From `~/.claude/CLAUDE.md`:

- **NEVER** delete source files.
- **NEVER** push tags, push to main, or merge to main locally.
- **NEVER** add `Co-authored-by` or any AI attribution to commits/PRs.
- **NEVER** use `double()` in specs — real model instances only.
- **NEVER** hand-roll serialization (`to_h`, `from_h`, etc.) —
  `lutaml-model` mappings only.
- **NEVER** use `require_relative` for library code — use `autoload`.
- **NEVER** use `send` for private methods, `instance_variable_get`/`set`,
  or `respond_to?` for type checks.
- Library packages have no side effects (no writes to install dir).
- **ALL** changes go through PRs.
