# TODO-07 — Local end-to-end verification

- **Priority:** P0
- **Repo:** all three (this verifies the integrated behavior)
- **Depends on:** TODO-01 through TODO-06 all merged locally (PRs not
  required to be merged on remote — local state is enough to test).

## Goal

Reproduce the CI pipeline locally and confirm:

1. metanorma-document HTML output renders correctly (TermNum headings,
   labels inside first `<p>`, CSS matches).
2. OIML STS XML converts without parity gaps.
3. OIML STS HTML renders correctly (Liquid templates, inlined SVG/CSS).
4. Parity validator reports PASS at 100%.
5. The two HTML outputs are visually consistent.

## Steps

```bash
# Use the renderer bundle (Gemfile.renderer)
export BUNDLE_GEMFILE=Gemfile.renderer

# 1. metanorma-document HTML
bundle exec ruby -e '
  require "metanorma/document"
  require "metanorma/oiml/document"
  xml = File.read("_site/documents/sts-guidelines/document.presentation.xml")
  doc = Metanorma::Oiml::Document::Root.from_xml(xml)
  File.write("_site/documents/sts-guidelines/document.html",
             Metanorma::Html::Generator.generate(doc))
'

# 2. OIML STS XML
bundle exec oiml-sts convert \
  _site/documents/sts-guidelines/document.presentation.xml \
  _site/documents/sts-guidelines/document.sts.xml

# 3. OIML STS HTML
bundle exec ruby -r metanorma/oiml/sts -e '
  xml = File.read(ARGV[0])
  out = Metanorma::Oiml::Sts::HtmlRenderer.render(xml)
  File.write(ARGV[1], out)
' _site/documents/sts-guidelines/document.sts.xml \
  _site/documents/sts-guidelines/document.sts.html

# 4. Parity
bundle exec ruby -r metanorma/oiml/sts -e '
  mn  = File.read(ARGV[0])
  sts = File.read(ARGV[1])
  report = Metanorma::Oiml::Sts::ParityValidator.validate(mn_html: mn, sts_html: sts)
  puts "PASS: #{report.pass?} | word: #{report.word_coverage_percent}% | para: #{report.paragraph_coverage_percent}%"
  puts "missing: H#{report.missing_headings.size} P#{report.missing_paragraphs.size} TC#{report.missing_table_cells.size} LI#{report.missing_list_items.size} N#{report.missing_notes.size} I#{report.missing_images.size}"
' _site/documents/sts-guidelines/document.html \
  _site/documents/sts-guidelines/document.sts.html
```

If `_site/documents/sts-guidelines/document.presentation.xml` is stale
or missing, regenerate it from the source AsciiDoc using the compile
stack (the metanorma container) — or skip the verification until a
fresh artifact is available.

## Acceptance

- [ ] All three artifacts exist and are non-empty:
  `_site/documents/sts-guidelines/document.html`,
  `document.sts.xml`, `document.sts.html`.
- [ ] Parity validator prints `PASS: true` with `para: 100.0%` and all
      missing counts at 0.
- [ ] Open the two HTML files in a browser; confirm:
  - TermNum headings render as headings (not paragraphs).
  - Note/Example labels are inside the first paragraph.
  - The dark-mode OIML logo shows in dark mode, light-mode in light.
  - The TOC, breadcrumb, and footer all populate.
  - No raw XML class names leak (`TermNum` is intentional; `Note`,
    `Example`, `ForewordTitle` etc. are not).
- [ ] No console errors in either HTML page.

## Out of scope

- The deployed `oimlsmart.org/specs` site — that's TODO-08 after CI
  runs on the merged PRs.
- Performance benchmarking (not a current concern).
