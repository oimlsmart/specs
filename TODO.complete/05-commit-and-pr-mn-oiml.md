# TODO-05 — Commit metanorma-oiml dirty WIP via PR

- **Priority:** P0
- **Repo:** `metanorma/metanorma-oiml`
- **Depends on:** TODO-04

## Current state

- Local branch: `main`
- Local HEAD: `c50c29c fix(sts): commit MathmlBuilder and add math_klass_for`
- Remote `origin/main` HEAD: `78450a5 feat: OIML STS converter with typed-model transformers, XSLT renderer, and parity validation`
- 5 commits ahead of origin, plus a large dirty WIP (~12 files, +700 lines).
- Specs are green (43 examples, 0 failures).

The 5 unpushed commits:

```
c50c29c fix(sts): commit MathmlBuilder and add math_klass_for
0dbb553 fix(sts): align term/note rendering with metanorma-document's actual markup
de08980 chore(deps): metanorma-document ~> 0.4.0
67dc7a8 feat(sts): parity validator handles renderer differences honestly
2745660 fix(sts): close the remaining content-parity gaps with Metanorma HTML
```

## Branch strategy

Two reasonable options:

### Option A — Single PR with everything (recommended)

Pros: simpler review, the 5 unpushed commits + dirty WIP are all part
of the same initiative (ship parity-100% OIML STS output).

Cons: PR is large (~700 dirty + ~300 committed = ~1000 lines).

1. Create branch off main:
   ```
   git checkout main && git pull --ff-only origin main
   git checkout -b feat/sts-parity-and-renderer
   ```
   Note: `git pull` will be a no-op since local main == origin main +
   the 5 unpushed commits. That's fine.

2. Commit the dirty WIP as one or more logical commits:
   - `feat(sts): defer paragraph footnotes, route table fns to tfoot`
   - `feat(sts): shield <mo>&lt;</mo> from lutaml-model's mixed_content parser`
   - `feat(sts): restore numbered term-note labels ("Note N to entry:")`
   - `feat(sts): tighten parity chrome — heading anchors, doc-title, list markers`

3. Push branch, open PR.

### Option B — Two PRs (unpushed commits first, then dirty WIP)

Only useful if the 5 unpushed commits need to land independently.
Looking at them, they are cohesive with the dirty WIP — Option A is
better.

## Commit messages for the dirty WIP

Each commit must NOT include `Co-authored-by` or any AI attribution
(global rule).

Use HEREDOCs:
```
git commit -m "$(cat <<'EOF'
feat(sts): defer paragraph footnotes and route table fns to tfoot

MN renders footnotes differently depending on context:
- Body paragraph fns become end-of-document <p> elements (one per fn).
- Table-cell fns become a <tfoot> with one <td> per fn.

This commit routes fn bodies accordingly during STS HTML rendering:
- @deferred_footnotes collects body fns; assembled at end of document.
- @table_fns (per-table stack via push/pop) routes cell fns into tfoot.
- The inline <sup> marker is always emitted in place.

Parity validator's "missing paragraphs" set drops to 0.
EOF
)"
```

## Acceptance

- [ ] Branch `feat/sts-parity-and-renderer` pushed.
- [ ] PR opened against `metanorma/metanorma-oiml` main.
- [ ] CI green (specs + rubocop).
- [ ] PR merged (rebase-merge per the kimi session's plan).

## Out of scope

- Updating `metanorma-document` dep version after the next
  metanorma-document release.
