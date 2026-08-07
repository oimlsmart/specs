# TODO-04 — Replace `require_relative` in lib/ with autoload

- **Priority:** P1

## Known violations (pre-existing)

| File                                                          | Lines | Statement                                       |
| ------------------------------------------------------------- | ----- | ----------------------------------------------- |
| `lib/metanorma-oiml.rb`                                       | 6     | `require_relative "metanorma/oiml/sts"`         |
| `lib/metanorma/oiml/sts/cli.rb`                               | 23    | `require_relative "../sts"`                     |
| `lib/metanorma/oiml/sts/cli.rb`                               | 38    | `require_relative "../sts"`                     |
| `lib/metanorma/oiml/sts/cli.rb`                               | 58    | `require_relative "../sts"`                     |
| `lib/metanorma/oiml/html.rb`                                  | 4     | `require_relative "document"`                   |
| `lib/metanorma/oiml/html.rb`                                  | 5     | `require_relative "html/renderer"`              |
| `lib/metanorma/oiml/sts/transformer/source_document.rb`       | 4     | `require_relative "../../document"`             |

## Strategy

Per `~/.claude/CLAUDE.md`:
> **NEVER** use `require_relative` for internal library code. Use
> Ruby `autoload` instead. Define autoload entries in the **immediate
> parent namespace's file** — create that file if it doesn't exist.

Concretely:
- `lib/metanorma-oiml.rb` (top-level entry) should declare
  `autoload :Sts, "metanorma/oiml/sts"` for the Sts namespace (or
  just `require "metanorma/oiml/sts"` since this is the entry file —
  entry files are allowed to require their direct children).
- `lib/metanorma/oiml/sts.rb` already declares autoloads for Cli,
  Transformer, etc. — no change needed.
- `lib/metanorma/oiml/sts/cli.rb` should drop its three
  `require_relative "../sts"` lines — the parent namespace is
  already loaded by the time `Cli` is autoloaded.
- `lib/metanorma/oiml/html.rb` should declare autoloads for
  `Document` and `Html::Renderer`, dropping the `require_relative`.
- `lib/metanorma/oiml/sts/transformer/source_document.rb` should
  drop `require_relative "../../document"` — autoload handles it.

## Acceptance

- [ ] `grep -rn "require_relative" lib/` returns zero hits.
- [ ] `bundle exec rspec` — all green.
- [ ] `bundle exec oiml-sts convert` still works end-to-end.

## Out of scope

- External `require` lines (e.g. `require "sts"`, `require "liquid"`) — those are fine.
