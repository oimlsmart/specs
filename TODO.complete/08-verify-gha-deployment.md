# TODO-08 — Verify GHA deployment

- **Priority:** P1
- **Repo:** `oimlsmart/specs`
- **Depends on:** TODO-03, TODO-05, TODO-06 all merged on remote.

## Goal

After the three PRs land on `main`, the `generate.yml` workflow runs on
push and re-deploys the GitHub Pages site. Confirm:

1. The workflow succeeds (compile + render + deploy).
2. The deployed site at `https://www.oimlsmart.org/specs/` shows the
   updated HTML (TermNum headings, labels inside first `<p>`, etc.).
3. No Code Alerts or warnings on GitHub.

## Steps

1. Watch the latest run:
   ```
   gh run watch
   ```
2. When it completes, list recent runs and confirm success:
   ```
   gh run list --workflow=generate.yml --limit=3
   ```
3. Curl the deployed HTML and grep for the new markup:
   ```
   curl -s https://www.oimlsmart.org/specs/documents/sts-guidelines/document.html \
     | grep -c '<h[23] class="TermNum"'
   # expect: >= 1
   ```
4. Compare against the staged sts HTML:
   ```
   curl -s https://www.oimlsmart.org/specs/documents/sts-guidelines/document.sts.html \
     | head -100
   ```

## Acceptance

- [ ] Latest `generate.yml` run on `main` is `success`.
- [ ] Deploy step completes (Pages URL printed).
- [ ] `curl` returns expected markup (TermNum heading present in
      `document.html`, inlined SVG logo present in `document.sts.html`).
- [ ] No alerts on the GitHub repo's Actions tab.

## Out of scope

- Custom domain DNS / TLS — that's GitHub Pages infra.
- Performance or Lighthouse scores.
