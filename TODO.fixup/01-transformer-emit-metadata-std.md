# TODO-01 — Transformer emits `MetadataStd` into `Front#std_meta`

- **Priority:** P0
- **Files:** `lib/metanorma/oiml/sts/transformer/model_builder.rb`,
  `lib/metanorma/oiml/sts/transformer/front_transformer.rb`.

## Why

OIML is not ISO. sts-ruby 0.6.0 ships both `MetadataIso` (→ `<iso-meta>`)
and `MetadataStd` (→ `<std-meta>`); the latter is the NISO-STS-correct
container for OIML.

## Scope

1. `ModelBuilder.iso_meta(...)` is renamed `ModelBuilder.std_meta(...)`
   and builds `Sts::NisoSts::MetadataStd` (was `MetadataIso`).
2. `ModelBuilder.front(...)` assigns the meta block to
   `Front#std_meta` (was `iso_meta`).
3. `FrontTransformer` calls the renamed builder.

The signature of the builder is unchanged — same kwargs
(`doc_identifier:`, `title:`, `pub_date:`, `permissions:`,
`custom_meta_group:`). `MetadataStd` exposes the same attributes
(`title_wrap`, `std_ident`, `permissions`, `pub_date`,
`custom_meta_group`).

## Acceptance

- [ ] `bundle exec oiml-sts convert` produces XML with `<std-meta>` and no `<iso-meta>`.
- [ ] All existing transformer specs pass.
- [ ] No `respond_to?`, no `instance_variable_get/set`, no `send` to private methods.
- [ ] No `require_relative` added.

## Out of scope

- Renderer updates — TODO-02.
- Specs — TODO-03.
