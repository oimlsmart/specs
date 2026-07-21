# TODO-04 — Audit metanorma-oiml dirty WIP for code quality

- **Priority:** P0
- **Repo:** `metanorma/metanorma-oiml`
- **Blocks:** TODO-05

## Context

Same standard as TODO-02, applied to the kimi session's dirty WIP in
`metanorma-oiml`. The dirty diff is much larger (~700 lines across 12
files) so this audit is non-trivial.

## Known existing violations (do NOT fix here, just be aware)

These were introduced in already-committed code before the kimi session:

- `lib/metanorma-oiml.rb:6` — `require_relative "metanorma/oiml/sts"`
- `lib/metanorma/oiml/sts/cli.rb:23,38,58` — three `require_relative`
- `lib/metanorma/oiml/html.rb:4,5` — `require_relative`
- `lib/metanorma/oiml/sts/transformer/source_document.rb:4,191` —
  `require_relative` and `instance_variable_get`
- `lib/metanorma/oiml/sts/validator.rb:37` — `respond_to?(:read)`
- `lib/metanorma/oiml/sts/html_renderer/ruby.rb:667` — `respond_to?(:mappings_for)`

These are tracked separately in TODO-99 — out of scope here. Just don't
introduce NEW violations.

## Scope — files in the dirty diff

- `Gemfile`
- `lib/metanorma/oiml/sts.rb`
- `lib/metanorma/oiml/sts/html_renderer.rb`
- `lib/metanorma/oiml/sts/html_renderer/ruby.rb` (+141 lines)
- `lib/metanorma/oiml/sts/parity_validator/normaliser.rb`
- `lib/metanorma/oiml/sts/parity_validator/report.rb`
- `lib/metanorma/oiml/sts/transformer/model_builder.rb`
- `lib/metanorma/oiml/sts/transformer/note_transformer.rb`
- `lib/metanorma/oiml/sts/transformer/paragraph_transformer.rb` (+249 lines)
- `lib/metanorma/oiml/sts/transformer/section_transformer.rb`
- `lib/metanorma/oiml/sts/transformer/table_transformer.rb`
- `lib/metanorma/oiml/sts/transformer/term_transformer.rb`
- `spec/sts/transformer/dl_transformer_spec.rb` (untracked)

## Audit checklist per file

- [ ] **html_renderer/ruby.rb** — the dirty diff adds:
  - `LT_SENTINEL` constant (U+E000 Unicode private-use char) for
    shielding `<mo>&lt;</mo>` from lutaml-model's mixed_content parser.
    Documented inline as a workaround. Acceptable while the upstream
    parser bug exists.
  - `@deferred_footnotes`, `@table_fns` instance collections for
    routing footnotes. Stateful visitor pattern — acceptable.
  - `mathml_to_html` does namespace stripping + reformatting. Pure
    function of input. Could be extracted to a `MathmlFormatter` class
    if it grows.
  - `body_title_paragraph` — builds `<p class="doc-title">` from meta.
  - `deferred_footnotes_html` — emits `<p class="footnote">` per
    deferred fn body.
  - `term()` now wraps children in `<p>` (was: direct render).
  - `table()` collects cell fns and emits `<tfoot>` — clean.
  - `note()` always injects label into first `<p>` — symmetric with
    metanorma-document's `NoteDrop.inject_label`.
  - `example()` mirrors note() with own-label detection.
  - `fn()` routes body to `@table_fns` or `@deferred_footnotes`.

- [ ] **transformer/paragraph_transformer.rb** (+249 lines) — the
      largest single addition. Verify:
  - Each transformer method is single-purpose (MECE).
  - No method exceeds ~40 lines (extract if larger).
  - No `respond_to?` introduced.
  - New helpers exposed via `private` where appropriate.

- [ ] **transformer/section_transformer.rb** (+125 lines) — section
      label/autonum extraction. Same checks.

- [ ] **transformer/table_transformer.rb** (+120 lines) — table
      caption + cell formatting. Same checks.

- [ ] **transformer/note_transformer.rb** — label handling changes.

- [ ] **transformer/term_transformer.rb** — `term_note_label` restored
      to numbered form ("Note N to entry:").

- [ ] **parity_validator/normaliser.rb, report.rb** — minor changes.

- [ ] **sts.rb** — check what changed.

## Acceptance

- [ ] No NEW `require_relative` in the dirty diff.
- [ ] No NEW `send(` calls to private methods.
- [ ] No NEW `instance_variable_get` / `instance_variable_set`.
- [ ] No NEW `respond_to?` type checks.
- [ ] `bundle exec rubocop lib/metanorma/oiml/sts` is clean.
- [ ] `bundle exec rspec spec/sts/` reports `0 failures` (currently 43/0).

## Out of scope

- Existing violations listed above (tracked in TODO-99 if we choose to
  fix them separately).
- Refactoring `html_renderer/ruby.rb` (740 lines) — the file is large
  but cohesive (one renderer class). Splitting it would scatter the
  DISPATCH table across files for no benefit right now.
