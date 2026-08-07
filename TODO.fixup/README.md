# TODO.fixup — Work tracker for the std-meta migration

The OIML STS toolkit currently emits `<iso-meta>` for front matter —
semantically wrong (OIML is not ISO). NISO STS V1.2's correct
container for OIML is `<std-meta>` (generic catch-all for non-ISO /
non-national / non-regional SDOs).

sts-ruby 0.6.0 ships everything we need — no subclassing required:

- `Sts::NisoSts::MetadataStd` — serializes as `<std-meta>`.
- `Sts::NisoSts::Front` — already declares `std_meta:` attribute of
  type `MetadataStd` (alongside the existing `iso_meta:`, `nat_meta:`,
  `reg_meta:`, `std_doc_meta:`).

So the migration is mechanical: change the transformer to build
`MetadataStd` and assign to `front.std_meta`, and update the
renderer's meta-node lookup to recognise `MetadataStd`.

## Active work

| #                                                          | Priority | Description                                                  |
| ---------------------------------------------------------- | -------- | ------------------------------------------------------------ |
| [01](01-transformer-emit-metadata-std.md)                 | P0       | Build `MetadataStd` and assign to `Front#std_meta` instead of `MetadataIso` / `Front#iso_meta`. |
| [02](02-renderer-find-meta-node-is-a.md)                   | P0       | `find_meta_node` uses `is_a?(Sts::NisoSts::MetadataIso)` plus `is_a?(Sts::NisoSts::MetadataStd)` — drops the hard-coded class-name list (OCP). |
| [03](03-specs.md)                                          | P0       | Spec: `<std-meta>` round-trips; front.std_meta populated.   |
| [04](04-require-relative-cleanup.md)                       | P1       | Replace `require_relative` in lib/ with autoload at the parent namespace. |
| [05](05-end-to-end-verify.md)                              | P0       | Local end-to-end: parity PASS, deployed XML has `<std-meta>`, title in nav. |

## Global rules in effect

From `~/.claude/CLAUDE.md`:
- **NEVER** use `require_relative` for library code — use `autoload` declared in the immediate parent namespace's file path.
- **NEVER** use `send` for private methods, `instance_variable_get`/`set`, or `respond_to?` for type checks.
- **NEVER** add `Co-authored-by` or AI attribution.
- **ALL** changes go through PRs.
