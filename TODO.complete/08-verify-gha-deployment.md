# TODO-08 — Verify GHA deployment

- **Priority:** P1
- **Repo:** `oimlsmart/specs`
- **Depends on:** TODO-03, TODO-05, TODO-06 all merged on remote.
- **Status:** ✅ DONE — 2026-07-22. All three PRs merged; main-branch CI green; Pages deployment successful.

## Outcome

### Main-branch CI run

Run [`29852551725`](https://github.com/oimlsmart/specs/actions/runs/29852551725):

- `compile` — ✓ (Metanorma container, AsciiDoc → presentation XML).
- `render` — ✓ (Ruby 3.4 + Gemfile.renderer; produced `document.html`, `document.sts.xml`, `document.sts.html`).
- `deploy` — ✓ (GitHub Pages, 11s).

### Deployed site

| URL                                                                   | HTTP | Bytes   | Notes                                                                                                                  |
| --------------------------------------------------------------------- | ---- | ------- | ---------------------------------------------------------------------------------------------------------------------- |
| `https://www.oimlsmart.org/specs/documents/sts-guidelines/document.html`    | 200  | 208,991 | Renders correctly. **However**, term numbers use the old `<p class="term-number">` markup (see Known Gaps below).     |
| `https://www.oimlsmart.org/specs/documents/sts-guidelines/document.sts.html` | 200  | 114,882 | 6 inlined `<svg>` (logos + icons), Tailwind 4 CDN, inlined CSS, TOC, breadcrumb — all correct.                          |
| `https://www.oimlsmart.org/specs/documents/sts-guidelines/document.sts.xml`  | 200  | (N/A)   | STS XML artifact.                                                                                                       |

### Known gaps

1. **TermNum heading markup not on the deployed `document.html`.** PR
   [#34](https://github.com/metanorma/metanorma-document/pull/34) landed
   on `metanorma/metanorma-document` main, but the latest released
   gem is still 0.5.0 (cut before #34). The `Gemfile.renderer` pins
   `metanorma-document >= 0.5.0`, so Bundler selects 0.5.0 — the
   TermNum heading work doesn't ship until the next release (0.5.1
   or 0.6.0).
   - **STS HTML is unaffected** — `metanorma-oiml`'s Liquid renderer
     has its own term-number rendering and is current.
   - **Workaround if needed before the next release**: vendor
     `metanorma-document` from origin/main the same way
     `metanorma-oiml` is vendored today. Not done here — needs a
     maintainer call.

2. **Note/Example labels still outside first `<p>`** in the deployed
   `document.html`. Same root cause as #1.

## Acceptance

- [x] Latest `generate.yml` run on `main` is `success`.
- [x] Deploy step completes (Pages URL printed).
- [x] `curl` returns 200 for both `document.html` and `document.sts.html`.
- [x] STS HTML has expected markup (inlined SVG, Tailwind, TOC, breadcrumb).
- [ ] `document.html` shows TermNum heading markup — **blocked on next metanorma-document gem release**.
