# TODO-01 — Fix the 6 failing specs in metanorma-document

- **Priority:** P0
- **Repo:** `metanorma/metanorma-document`
- **Blocks:** TODO-03 (PR cannot land with red specs)
- **State:** Local working tree is dirty with the kimi session's final-turn WIP
  (TermNum changed from `<p class="term-number">` paragraph to
  `<h{level} class="TermNum">` heading; note/example labels injected inside
  the first `<p>`). Specs were not updated to match.

## Context

The dirty WIP aligns metanorma-document with the isodoc convention used by
every Metanorma fixture under `spec/fixtures/iso/is/`:

```
<div id="term-paddy"><h2 class="TermNum" id="…">3.1</h2></div>
```

The previous renderer emitted `<p class="term-number">3.1</p>`. Six specs
still assert the old paragraph-class shape and now fail.

## Scope

Update exactly these specs to match the new markup. Do not touch the
renderer, the templates, or the CSS — they are correct (parity PASS at
100% against metanorma-oiml's STS HTML in the kimi session's final run).

| Spec                                                            | What to change                                                                                          |
| --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `spec/metanorma/html/generator_spec.rb:41`                      | `page.css(".term-number")` → `page.css(".TermNum")`                                                     |
| `spec/metanorma/html/renderer/class_ownership_spec.rb:25`       | Remove `TermNum` from `XML_CLASS_NAMES` (it is now intentionally emitted, matching isodoc).             |
| `spec/metanorma/html/renderer/class_ownership_spec.rb:70`       | Rewrite the example: `renders TermNum as a heading (isodoc convention)` and assert `h2.TermNum` exists. |
| `spec/metanorma/html/renderer/liquid_templates_spec.rb:155`     | `_term_number.html.liquid` test: assert `TermNum` and `<h` (or pass `level` and assert `<h2`).          |
| `spec/metanorma/html/renderer/section_rendering_spec.rb:42`     | `.term-name, .term-number` → `.term-name, .TermNum`                                                     |
| `spec/metanorma/html/renderer/standard_renderer_spec.rb:23`     | `.term-number` → `.TermNum`                                                                             |

## Acceptance

- [ ] `bundle exec rspec spec/metanorma/html/` reports `0 failures`.
- [ ] No new rubocop offenses in the touched files.
- [ ] The full suite (`bundle exec rspec`) still has the same green count
      as before, plus the 6 newly-green specs.

## Out of scope

- Renaming other XML-originated class names (`Note`, `Example`, etc.).
  Those are not emitted by mn-document; they only appear in fixtures.
- Touching the renderer, templates, or CSS.
