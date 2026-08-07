# TODO-05 — End-to-end verification

- **Priority:** P0

## Steps

1. Run the renderer pipeline locally against the sts-guidelines
   presentation XML:
   ```bash
   bundle exec oiml-sts convert \
     _site/documents/sts-guidelines/document.presentation.xml \
     /tmp/std-meta.sts.xml
   ```
2. Confirm `/tmp/std-meta.sts.xml` contains `<std-meta>` and no
   `<iso-meta>`:
   ```bash
   grep -c "<std-meta>" /tmp/std-meta.sts.xml   # expect: 1
   grep -c "<iso-meta>" /tmp/std-meta.sts.xml   # expect: 0
   ```
3. Render STS HTML, open in browser, confirm:
   - Document title shows in the header nav.
   - OIML SMART logos in light and dark mode.
   - Foreword/Introduction render as normal sections.
   - Term TOC entries show "3.1 NISO STS" (number + name).
   - Body content sits beside the TOC, not behind it.
4. Run the parity validator:
   ```bash
   bundle exec ruby -r metanorma/oiml/sts -e '
     report = Metanorma::Oiml::Sts::ParityValidator.validate(
       mn_html:  File.read("/tmp/mn.html"),
       sts_html: File.read("/tmp/std-meta.sts.html"),
     )
     puts "PASS: #{report.pass?} | para: #{report.paragraph_coverage_percent}%"
   '
   ```
5. Run the full spec suite: `bundle exec rspec spec/sts/` — 46+/0.

## Acceptance

- [ ] All four grep counts correct.
- [ ] Visual checks pass.
- [ ] Parity validator: PASS at 100% paragraph coverage.
- [ ] Spec suite green.
