# TODO-99 — (Optional backlog) Code quality cleanup in metanorma-oiml

- **Priority:** P2
- **Repo:** `metanorma/metanorma-oiml`
- **Status:** Backlog — not required for the immediate work to land.

## Known pre-existing violations (introduced before the kimi session)

These are tracked here so we don't lose them. They do NOT block
TODO-01 through TODO-08.

### `require_relative` (should be `autoload`)

| File                                                         | Line | Statement                                            |
| ------------------------------------------------------------ | ---- | ---------------------------------------------------- |
| `lib/metanorma-oiml.rb`                                      | 6    | `require_relative "metanorma/oiml/sts"`              |
| `lib/metanorma/oiml/sts/cli.rb`                              | 23   | `require_relative "../sts"`                          |
| `lib/metanorma/oiml/sts/cli.rb`                              | 38   | `require_relative "../sts"`                          |
| `lib/metanorma/oiml/sts/cli.rb`                              | 58   | `require_relative "../sts"`                          |
| `lib/metanorma/oiml/html.rb`                                 | 4    | `require_relative "document"`                        |
| `lib/metanorma/oiml/html.rb`                                 | 5    | `require_relative "html/renderer"`                   |
| `lib/metanorma/oiml/sts/transformer/source_document.rb`      | 4    | `require_relative "../../document"`                  |

### `instance_variable_get` (breaks encapsulation)

| File                                                                    | Line | Statement                                       |
| ----------------------------------------------------------------------- | ---- | ----------------------------------------------- |
| `lib/metanorma/oiml/sts/transformer/source_document.rb`                 | 191  | `node.class.instance_variable_get(:@attributes)` |

### `respond_to?` (poor type check)

| File                                                          | Line | Statement                                 |
| ------------------------------------------------------------- | ---- | ----------------------------------------- |
| `lib/metanorma/oiml/sts/validator.rb`                         | 37   | `x.respond_to?(:read)`                    |
| `lib/metanorma/oiml/sts/html_renderer/ruby.rb`                | 667  | `klass.respond_to?(:mappings_for)`        |

## Refactor plan (if/when we do this)

1. Replace each `require_relative` with an `autoload` declared in the
   immediate parent namespace's file. For example, move the entries
   that live in `lib/metanorma-oiml.rb` to a new
   `lib/metanorma/oiml.rb` that just declares the autoloads.
2. Drop the `instance_variable_get(:@attributes)` in
   `source_document.rb` — lutaml-model exposes attribute metadata
   through a public API (e.g., `klass.attributes`).
3. Replace `respond_to?(:read)` with `is_a?(IO)` or `defined?(@read)`
   depending on the actual intent. For XML input acceptance, accept
   `String | Pathname | #to_path | IO` and test with `is_a?`.
4. Replace `respond_to?(:mappings_for)` with `is_a?(Class)` and a
   type check against `Lutaml::Model::Serializable` descendants.

## Out of scope for now

This TODO exists to capture the violations so they aren't lost.
Do NOT fix them as part of the kimi session continuation — they are
unrelated to the TermNum/parity work and bundling them would
inflate the PRs.
