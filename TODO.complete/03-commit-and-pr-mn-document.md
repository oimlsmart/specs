# TODO-03 — Commit metanorma-document dirty WIP via PR

- **Priority:** P0
- **Repo:** `metanorma/metanorma-document`
- **Depends on:** TODO-01, TODO-02

## Current state

- Local branch: `main`
- Local HEAD: `9000a74 test: convert labeled-list specs to expect() syntax`
- Remote `origin/main` HEAD: `16bdab6 Bump metanorma-document to 0.5.0`
  (one commit ahead of local — the 0.5.0 release was cut without the
  dirty WIP).
- Dirty WIP: 9 modified files (~58 lines), described in TODO-02.
- 6 specs fail (TODO-01 fixes them).

The 0.5.0 release on RubyGems does NOT include the TermNum-heading work.
Once this PR merges, the next release (presumably 0.5.1 or 0.6.0) will
ship it.

## Branch strategy

1. **Pull origin/main into local main** — bring the 0.5.0 bump into
   local so the dirty WIP applies on top of it.
   ```
   git fetch origin
   git checkout main && git pull --ff-only origin main
   ```
2. **Create a feature branch off main** — never commit on main per
   global rules.
   ```
   git checkout -b fix/termnum-heading-markup
   ```
3. **Stage the dirty WIP + the spec fixes from TODO-01** as a single
   commit (the renderer/template/CSS changes + their spec updates belong
   together — reviewers see the why and the how in one diff).
4. **Push the branch and open a PR** with the body explaining:
   - TermNum now renders as `<h{level}>` heading, matching the isodoc
     fixtures under `spec/fixtures/iso/is/`.
   - Note/Example labels are injected inside the first `<p>` (isodoc
     convention).
   - CSS `.term-number` rules renamed `.TermNum`.

## Commit message

```
feat(html): render TermNum as a heading, inject note/example labels into first <p>

Aligns metanorma-document's HTML output with the isodoc convention used
by every fixture in spec/fixtures/iso/is:

- Term numbers render as <h2>/<h3> headings with class "TermNum"
  (was: <p class="term-number">). Heading level is section_level + 1,
  matching isodoc's term_header postprocess.
- NOTE and EXAMPLE labels open the first paragraph instead of floating
  outside it, so the visible text matches isodoc's
  <p><span class="note-label">NOTE</span> text…</p> shape.
- CSS .term-number rules renamed to .TermNum across base and component
  stylesheets.

Parity with metanorma-oiml's STS HTML reporter is unchanged at 100%.
```

No `Co-authored-by`, no AI attribution.

## Acceptance

- [ ] Branch `fix/termnum-heading-markup` pushed.
- [ ] PR opened against `metanorma/metanorma-document` main.
- [ ] CI on the PR is green (specs + rubocop).
- [ ] PR merged (rebase-merge per the kimi session's plan).

## Out of scope

- Cutting a new gem release. The next version bump happens after this
  lands — separately, by the maintainer.
