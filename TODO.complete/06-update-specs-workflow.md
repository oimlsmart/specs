# TODO-06 — Update specs workflow for the new metanorma-oiml API

- **Priority:** P0
- **Repo:** `oimlsmart/specs` (current directory)
- **Depends on:** TODO-05 (the new class name only exists in metanorma-oiml after the PR lands)
- **Blocks:** TODO-08 (CI will fail without this)

## Current state

- `.github/workflows/generate.yml` is currently green on `main` ONLY
  because it vendors `metanorma-oiml` from `origin/main` (which still
  uses the old `Metanorma::OimlDocument` class name and the XSLT
  renderer).
- The moment TODO-05 merges, the workflow breaks.

## Stale references

| Line                                                                                                  | Issue                                                                                                | Fix                                                                                                         |
| ----------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `Metanorma::OimlDocument::Root.from_xml(...)`                                                         | Class renamed to `Metanorma::Oiml::Document::Root` in `metanorma-oiml`.                             | Replace.                                                                                                    |
| `Metanorma::Oiml::Sts::HtmlRenderer.render(..., renderer: :xslt)`                                     | XSLT renderer was dropped; renderer is Liquid-only. The kwarg is silently swallowed by `**`.         | Drop the kwarg.                                                                                             |
| `cp "$GEMDIR/vendor/xslt/oiml-logo.svg" "$doc/"`                                                      | The path doesn't exist; the Liquid renderer inlines the SVG via `assets_dir`.                       | Delete the copy step entirely.                                                                              |
| `bundle exec oiml-sts convert "$doc/document.presentation.xml" "$doc/document.sts.xml"`               | Correct — `oiml-sts` exe is provided by metanorma-oiml.                                              | No change.                                                                                                  |

## Updated render step

```yaml
- name: Generate site HTML (metanorma-document) and OIML STS outputs
  env:
    BUNDLE_GEMFILE: Gemfile.renderer
  run: |
    doc=_site/documents/sts-guidelines
    bundle exec ruby -e \
      'require "metanorma/document"; require "metanorma/oiml/document"; doc = Metanorma::Oiml::Document::Root.from_xml(File.read(ARGV[0])); File.write(ARGV[1], Metanorma::Html::Generator.generate(doc))' \
      "$doc/document.presentation.xml" "$doc/document.html"
    bundle exec oiml-sts convert \
      "$doc/document.presentation.xml" "$doc/document.sts.xml"
    bundle exec ruby -r metanorma/oiml/sts -e \
      'File.write(ARGV[1], Metanorma::Oiml::Sts::HtmlRenderer.render(File.read(ARGV[0])))' \
      "$doc/document.sts.xml" "$doc/document.sts.html"
    ls -la "$doc/"
```

### Notes

- The `require "metanorma/oiml/document"` is needed because
  `require "metanorma/document"` doesn't pull in flavor classes.
  Verify the actual file path before committing — it might be
  `require "metanorma/oiml"` (the gem's top-level entrypoint).
- Drop the `GEMDIR=$(bundle exec ruby ...)` line entirely; nothing
  uses it anymore.
- The `ls -la "$doc/"` is debugging leftover — keep or drop based on
  preference. Keeping it gives a useful artifact listing in CI logs.

## Acceptance

- [ ] `.github/workflows/generate.yml` updated as above.
- [ ] Workflow YAML is syntactically valid (`yq` or `actionlint` clean).
- [ ] Branch pushed, PR opened (do NOT commit on main).

## Out of scope

- Bumping `metanorma-oiml` from path source to a versioned gem dep.
  Path source via `vendor/metanorma-oiml` clone is fine until the next
  gem release.
- Splitting the workflow into more jobs.
