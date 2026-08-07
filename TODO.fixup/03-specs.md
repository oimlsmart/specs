# TODO-03 — Specs for the `<std-meta>` migration

- **Priority:** P0
- **Files:** `spec/sts/std_meta_spec.rb` (new),
  `spec/sts/transformer/front_transformer_spec.rb` (new or extend).

## Scope

1. **Round-trip spec** — `Sts::NisoSts::MetadataStd` parses and
   serialises `<std-meta>`:
   ```ruby
   xml = <<~XML
     <std-meta>
       <title-wrap><main>OIML X 999:2026 title</main></title-wrap>
       <permissions>
         <copyright-statement>© 2026 OIML</copyright-statement>
       </permissions>
       <pub-date>2026</pub-date>
     </std-meta>
   XML
   meta = Sts::NisoSts::MetadataStd.from_xml(xml)
   expect(meta.title_wrap.main).to eq("OIML X 999:2026 title")
   expect(meta.to_xml).to include("<std-meta>")
   expect(meta.to_xml).not_to include("<iso-meta>")
   ```

2. **Front routing spec** — `Sts::NisoSts::Front` parses `<std-meta>`
   into `#std_meta` (not `#iso_meta`):
   ```ruby
   front = Sts::NisoSts::Front.from_xml("<front><std-meta>…</std-meta></front>")
   expect(front.std_meta).to be_a(Sts::NisoSts::MetadataStd)
   expect(front.iso_meta).to be_nil
   ```

3. **Transformer spec** — `Metanorma::Oiml::Sts::Transformer::FrontTransformer`
   produces a Front whose `std_meta` (not `iso_meta`) is set:
   ```ruby
   front = FrontTransformer.new(...).transform(source)
   expect(front.std_meta).to be_a(Sts::NisoSts::MetadataStd)
   expect(front.iso_meta).to be_nil
   ```

4. **End-to-end:** `Metanorma::Oiml::Sts.convert(source_xml)` emits
   `<std-meta>` and no `<iso-meta>`.

## Acceptance

- [ ] All four spec cases above pass.
- [ ] No new rubocop offenses.
- [ ] Real model instances (no `double()`).
