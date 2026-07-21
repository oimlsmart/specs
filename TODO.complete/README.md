# TODO.complete — Work tracker for the kimi session continuation

This directory holds the granular TODOs that complete the work paused
in the kimi session export (`kimi-export-session_-20260721-150805.md`).
Each file is self-contained: priority, scope, acceptance criteria.

## Completed work

| #                                                                | P   | Status                                                            | Repo                                  | Description                                                           |
| ---------------------------------------------------------------- | --- | ----------------------------------------------------------------- | ------------------------------------- | --------------------------------------------------------------------- |
| [01](01-fix-mn-document-specs.md)                                | P0  | ✅ done                                                           | `metanorma/metanorma-document`        | Fix the 6 failing specs (TermNum heading markup).                    |
| [02](02-audit-mn-document-quality.md)                            | P0  | ✅ done                                                           | `metanorma/metanorma-document`        | Audit dirty WIP for quality standards.                                |
| [03](03-commit-and-pr-mn-document.md)                            | P0  | ✅ merged [#34](https://github.com/metanorma/metanorma-document/pull/34) | `metanorma/metanorma-document`        | TermNum heading, label injection, ReDoS fix.                          |
| [04](04-audit-mn-oiml-quality.md)                                | P0  | ✅ done                                                           | `metanorma/metanorma-oiml`            | Audit dirty WIP for quality standards.                                |
| [05](05-commit-and-pr-mn-oiml.md)                                | P0  | ✅ merged [#20](https://github.com/metanorma/metanorma-oiml/pull/20) | `metanorma/metanorma-oiml`            | STS converter rewrite + dirty WIP, single squash commit.              |
| [06](06-update-specs-workflow.md)                                | P0  | ✅ merged [#10](https://github.com/oimlsmart/specs/pull/10)       | `oimlsmart/specs`                     | Workflow + Gemfile.renderer updates.                                  |
| [07](07-local-e2e-verification.md)                               | P0  | ✅ done — parity PASS at 100%                                     | all                                   | Local end-to-end run; confirm parity 100%.                            |
| [08](08-verify-gha-deployment.md)                                | P1  | ✅ done — main-branch CI green, Pages deployed                    | `oimlsmart/specs`                     | Verify GHA deployment is green and site is correct.                   |

## Backlog

| #                                                                | P   | Status | Repo                                  | Description                                                           |
| ---------------------------------------------------------------- | --- | ------ | ------------------------------------- | --------------------------------------------------------------------- |
| [99](99-oiml-existing-quality-debt.md)                           | P2  | open   | `metanorma/metanorma-oiml`            | Pre-existing `require_relative`, `instance_variable_get`, `respond_to?` violations. |

## Followups for the maintainer

- **Cut `metanorma-document` 0.5.1 (or 0.6.0)** to ship the TermNum
  heading markup. Until then, the deployed `document.html` shows the
  old `<p class="term-number">` shape.
- **Cut `metanorma-oiml` first stable release** once the converter
  has soaked a bit. The gemspec is currently `0.0.x` (dev versions).
- **Address TODO-99** quality debt in a dedicated cleanup PR (no
  functional pressure).

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
