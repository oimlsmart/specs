# TODO-02 — Audit metanorma-document dirty WIP for code quality

- **Priority:** P0
- **Repo:** `metanorma/metanorma-document`
- **Blocks:** TODO-03

## Context

Per the global rules (CLAUDE.md), library code must:

1. Avoid `require_relative` and any `require "<internal/path>"` — use
   Ruby `autoload` declared in the immediate parent namespace's file.
2. Never call private methods via `send`.
3. Never read or write another object's instance variables via
   `instance_variable_get` / `instance_variable_set`.
4. Never use `respond_to?` for type checks — use `is_a?` or design the
   type hierarchy so the check is unnecessary.

The kimi session's final-turn dirty WIP in `metanorma-document` is small
enough that this audit is a quick gate, not a refactor.

## Scope — files in the dirty diff

- `lib/metanorma/html/drops/note_drop.rb` — adds `inject_label` class method
- `lib/metanorma/html/drops/example_drop.rb` — calls `NoteDrop.inject_label`
- `lib/metanorma/html/standard_renderer.rb` — adds `level:` keyword
- `lib/metanorma/html/templates/_note.html.liquid` — drops standalone label span
- `lib/metanorma/html/templates/_example.html.liquid` — same
- `lib/metanorma/html/templates/_term_number.html.liquid` — emits `<h{level}>`
- `data/stylesheets/base/_dark.css`, `_print.css`, `components/term.css`
  — rename `.term-number` → `.TermNum`

## Audit checklist

- [ ] No `require_relative` added (the dirty diff should not touch `require`).
- [ ] No `send(` calls added.
- [ ] No `instance_variable_get` / `instance_variable_set` added.
- [ ] No `respond_to?` added.
- [ ] `NoteDrop.inject_label` is a pure function (string in, string out) —
      if it grows, extract to a dedicated `LabelInjector` service class.
- [ ] `example_drop.rb` calling `NoteDrop.inject_label` is acceptable
      (it's a shared utility for both drops). If the logic diverges, lift
      to a separate module and include in both drops.
- [ ] `render_term_number(term, level:)`, `render_term(term, level:)`
      follow the OCP — added keyword args, no breaking change to
      existing callers.
- [ ] Templates pass `level` correctly so `<h{level}>` renders `<h2>` for
      top-level terms (not `<h>` which the failing spec exposed).

## Acceptance

- [ ] All checks above pass.
- [ ] `bundle exec rubocop lib/metanorma/html/drops lib/metanorma/html/standard_renderer.rb`
      is clean on the touched files.

## Out of scope

- Pre-existing violations in other files (e.g.,
  `lib/metanorma/un_document/un_text_element.rb:93` uses
  `instance_variable_get`). These are existing tech debt, not introduced
  by this work. Flag separately if needed.
