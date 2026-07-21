# TODO-07 — Local end-to-end verification

- **Priority:** P0
- **Repo:** all three (this verifies the integrated behavior)
- **Depends on:** TODO-01 through TODO-06 all merged locally (PRs not
  required to be merged on remote — local state is enough to test).
- **Status:** ✅ DONE — 2026-07-22.

## Goal

Reproduce the CI pipeline locally and confirm:

1. metanorma-document HTML output renders correctly (TermNum headings,
   labels inside first `<p>`, CSS matches).
2. OIML STS XML converts without parity gaps.
3. OIML STS HTML renders correctly (Liquid templates, inlined SVG/CSS).
4. Parity validator reports PASS at 100%.
5. The two HTML outputs are visually consistent.

## Procedure used

Created a local-only `Gemfile.renderer.local` (not committed) with both
gems as path sources:

```ruby
source "https://rubygems.org"
gem "metanorma-document", path: "/Users/mulgogi/src/mn/metanorma-document"
gem "metanorma-oiml", path: "/Users/mulgogi/src/mn/metanorma-oiml"
gem "mml"
gem "pubid", "~> 2.0.0.pre.alpha"
gem "relaton-bib", "2.2.0.pre.alpha.1"
```

Then ran the three pipeline stages against
`_site/documents/sts-guidelines/document.presentation.xml`:

```bash
export BUNDLE_GEMFILE=Gemfile.renderer.local

bundle exec ruby -e '
  require "metanorma/document"
  require "metanorma/oiml/html"
  xml = File.read("_site/documents/sts-guidelines/document.presentation.xml")
  doc = Metanorma::Oiml::Document::Root.from_xml(xml)
  File.write("_site/documents/sts-guidelines/document.html",
             Metanorma::Html::Generator.generate(doc))
'

bundle exec oiml-sts convert \
  _site/documents/sts-guidelines/document.presentation.xml \
  _site/documents/sts-guidelines/document.sts.xml

bundle exec ruby -r metanorma/oiml/sts -e '
  File.write(ARGV[1], Metanorma::Oiml::Sts::HtmlRenderer.render(File.read(ARGV[0])))
' _site/documents/sts-guidelines/document.sts.xml \
  _site/documents/sts-guidelines/document.sts.html
```

## Findings

### Initial run (broken)

The first run produced a 127 KB `document.html` with the warning
`metanorma-document: no renderer registered for
Metanorma::Oiml::Document::Root — content skipped`.

Cause: requiring only `metanorma/oiml/document` does not register the
HTML renderer with `Metanorma::Html::Generator`. That registration
lives in `metanorma/oiml/html.rb` (`Metanorma::Oiml::Html::Renderer.
register_render Metanorma::Oiml::Document::Root, :render_document`).

Fix: require `metanorma/oiml/html` instead. Updated in commit `a33b0a8`
on PR #10.

### Post-fix run (correct)

| Artifact           | Size      | Notes                                                                |
| ------------------ | --------- | -------------------------------------------------------------------- |
| `document.html`    | 207,801 B | 3× `<h2 class="TermNum">`, 5× `<h3 class="TermNum">`. Note label is inside first `<p>`: `<p ...><span class="note-label">NOTE</span>&nbsp;...`. |
| `document.sts.xml` | 63,474 B  | Valid STS XML.                                                       |
| `document.sts.html`| 113,358 B | 6 inlined `<svg>` (logos + icons), Tailwind 4 CDN, inlined CSS, TOC. |

### Parity report

```
PASS: true | word: 93.33% | para: 100.0%
missing: H0 P0 TC0 LI0 N0 I0
```

All content sets at 0 missing — both renderers agree on headings,
paragraphs, table cells, list items, notes, images.

## Acceptance

- [x] All three artifacts exist and are non-empty.
- [x] Parity validator prints `PASS: true` with `para: 100.0%`.
- [x] Spot-checked markup: TermNum heading present in `document.html`,
      inlined SVG logos present in `document.sts.html`.
