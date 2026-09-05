# Production build for essentapro.com — 4 September 2026

**This branch is not the preview site.** It is a one-off build artefact, parked here so it has a
download URL. Nothing serves from it, and it is not part of the repo's normal history.

- **GitHub Pages serves `main`, not this branch.** Pushing here changed nothing about
  <https://tovmaskayuf.github.io/>.
- **Do not merge this branch into `main`.** `main` is the *preview* build — deliberately closed
  to search engines — and this is the *production* build for a different origin. Mixing them is
  exactly the mistake this build exists to correct.

## What it is

A static export of `tovmaskayuf/essenta-pro` at `cdbc75c`, built with:

```
NEXT_PUBLIC_SITE_URL=https://essentapro.com npm run check
```

214 files. Upload the contents at the **domain root**, so `/uk/` sits directly under it.

> ⚠ Downloading this branch as a ZIP from GitHub wraps everything in a folder named
> `tovmaskayuf.github.io-essentapro-com-build/`. Go **one level in** before uploading — the
> `uk`, `_next` and `video` folders must land at the root of the site, not inside a wrapper.

## Why it exists

essentapro.com was populated on 2026-09-04 by copying the GitHub Pages preview tree as it stood
on 18 August — verified, not guessed: the pages served there carry build id
`lgjc2wkpPsM9rzU6UgvlW`, which is preview commit `a21c11c`'s own artefact. That copy was eight
commits stale, and it carried the preview's flags onto the live domain.

This build fixes both halves of that:

- The Ukrainian "where to buy" lists **34 shops, no distributors** (the copy showed 54, including
  all eight distributors, three other markets' shops, and nine shops since deleted).
- `canonical`, `og:url`, every `hreflang` and all of `sitemap.xml` name **essentapro.com**. Zero
  references to the preview host remain.

## ⚠ `robots.txt` still says `Disallow: /` — deliberately

Do not "fix" it by hand; the next build overwrites it anyway. `/uk/privacy/` and
`/uk/cookie-policy/` are still English `TODO_LEGAL:` placeholders, and opening the site to search
engines while placeholder legal text is published under the brand's name is not acceptable.

When the legal copy lands, rebuild with `NEXT_PUBLIC_ALLOW_INDEXING=true` added. One variable,
one rebuild.

## Verified before pushing

- `npm run check` passes end to end — validate, typecheck, lint, format, build, QA
  (24 pages, 193 internal links, 746 assets).
- All 7 locales (`uk en hy ka pl lv lt`) drive clean in a headless browser: no page errors, no
  broken images, no horizontal overflow at 320 / 360 / 390 / 414 / 640 px.
- Deployment instructions for whoever uploads it, in Ukrainian: `INSTRUKCIYA-essentapro.txt`
  in this branch.

## This is a stopgap

Hand-carrying build folders is what caused the original problem. The durable fix is the
`Weekly rebuild` workflow in `essenta-pro`: it already builds and uploads `out/` as a downloadable
artifact on every run. It needs the repository variable `NEXT_PUBLIC_SITE_URL` set, and it needs
`essenta-pro` PR #3 merged first — otherwise it dies at `npm ci`.
