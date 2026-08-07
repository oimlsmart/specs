# TODO-02 — `find_meta_node` uses `is_a?` (OCP-compliant)

- **Priority:** P0
- **File:** `lib/metanorma/oiml/sts/html_renderer/ruby.rb`.

## Why

The current `find_meta_node` matches by demodulized class name
against a hard-coded list:

```ruby
return node if %w[MetadataIso IsoMeta StdMeta RegMeta NatMeta].include?(name)
```

This is OCP-violating — every new metadata class (e.g. `MetadataStd`)
needs the list updated. Replace with `is_a?` checks. The sts-ruby
hierarchy is the canonical type tree; subclasses are recognised
automatically.

## Scope

Replace the hard-coded list with:

```ruby
def meta_node?(node)
  node.is_a?(::Sts::NisoSts::MetadataIso) ||
    node.is_a?(::Sts::NisoSts::MetadataStd)
end
```

(`MetadataIso` covers `<iso-meta>`; `MetadataStd` covers `<std-meta>`.
`RegMeta` and `NatMeta` inherit from `Lutaml::Model::Serializable`
directly, not from either — if we ever need to recognise them, add
them explicitly. OIML doesn't emit them today.)

`find_meta_node` becomes:

```ruby
def find_meta_node(node)
  return node if meta_node?(node)
  return nil unless node.is_a?(Lutaml::Model::Serializable)
  # … walk children …
end
```

## Acceptance

- [ ] `find_meta_node` returns the `MetadataStd` instance for an OIML STS doc.
- [ ] Existing renderer specs pass.
- [ ] No `respond_to?` for type checks (uses `is_a?`).

## Out of scope

- Transformer changes — TODO-01.
- Specs — TODO-03.
