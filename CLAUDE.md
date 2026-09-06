# Essenta PRO — preview build

## What this repository is

A **built static export of a Next.js App Router site — generated output only.** No source, no
`package.json`, no content files.

Live at `https://tovmaskayuf.github.io/`, served by GitHub Pages from the default branch.
Every page ships `noindex, nofollow` and `robots.txt` disallows all crawling, deliberately —
the content is not approved for launch.

## ⚠ Do not edit anything here. It is disposable again.

**This tree is now generated wholesale from the private source repository**
(`tovmaskayuf/essenta-pro`) and replaced on every deploy. Anything you change here will be
destroyed by the next one, without warning and without a merge conflict to notice.

That was not always true, and the history is worth knowing. For a period this repo carried
work that existed **only** here — approved Ukrainian copy, mobile layout fixes, retailer
logos, two performance fixes — patched directly into the generated artefacts, because the
source had none of it. This file used to document how to do that patching. **All of it has
since been reconciled into the source**, which now leads on every count, so the patching
workflow is gone and so are the instructions for it. Look in `git log` before this commit if
you ever need them back.

Two of those fixes are worth naming, because they are easy to undo by accident in the source
and their absence is invisible here:

- **Only genuinely above-the-fold images may carry Next's `priority` flag.** It emits a
  `<link rel=preload>`, and six product stills inside closed `<details>` panels were being
  fetched before first paint.
- **The three above-the-fold fonts are preloaded** — Open Sauce, TT Norms and Open Sans. It
  takes all three because Open Sauce carries no Cyrillic, so a Ukrainian headline falls
  through to TT Norms mid-word.

## ⚠ The site is LIVE at essentapro.com — and what is live is not this tree

Since 2026-09-04 this build has a public sibling: <https://essentapro.com/>, which the client is
using for in-store consultations and for advertising. Do not assume the two agree. They do not.

`essentapro.com` serves preview commit `a21c11c` (18 Aug) — **the same build artefact, build id
`lgjc2wkpPsM9rzU6UgvlW`**, so the "archive" that seeded it was a copy of this tree taken that
day, not a build. It is eight commits behind. The symptom the client reported: the Ukrainian
where-to-buy lists **54 logos where the current build lists 34** — all eight distributors, three
foreign shops (Allegro, Dr. Max+, NuKo) and nine shops since deleted from the dataset (Antoshka,
Auchan, Kasta, Parfums UA, Prostor, Varus, Eettinen Luksus, Magaziin, Sahver). Everything from
`9cbcc56` on is absent there: the per-market filter, the owner's shop order, the signed Georgian
and Armenian, `lv` and `lt`.

### 🛑 Do not fix it by copying this tree onto that host — that is what caused it

This is a PREVIEW build and it carries the preview's flags wherever it is copied:

- `robots.txt` says `Disallow: /` and every page carries `noindex, nofollow`, so essentapro.com
  is invisible to every search engine — on a domain bought to advertise from.
- `canonical`, `og:url`, every `hreflang` and all of `sitemap.xml` name
  `https://tovmaskayuf.github.io/…`. The real domain's share cards and canonical signals
  currently point at the unindexed preview.

Both are baked in at build time (source `DEPLOY.md` §2), so the only fix is a rebuild from source
against the real origin — never a copy:

```
NEXT_PUBLIC_SITE_URL=https://essentapro.com npm run check
# and NEXT_PUBLIC_ALLOW_INDEXING=true once the legal copy below has landed
```

⚠ **`npm ci` fails on the final source branch** — `package-lock.json` is out of sync with
`package.json` (`@swc/helpers@0.5.23` missing). `npm install` builds fine. `DEPLOY.md` §1 still
says `npm ci`.

⚠ **`/uk/privacy/` and `/uk/cookie-policy/` are live on essentapro.com as English placeholders** —
20 `TODO_LEGAL:` markers on the privacy page alone. That is equally true of THIS tree, so
redeploying does not fix it and only the legal team's copy will. Turning indexing on before it
lands publishes placeholder legal text under the brand's name.

### The source leads, and a rebuild reproduces this tree exactly

Verified 2026-09-04 against `essenta-pro` branch `claude/armenian-page-translations-ckefjr`
(`cdbc75c`): 214 files, 181 byte-identical, the other 31 differing only in the build id and in the
build date the tube-life estimate counts from. `npm run qa` passes — 24 pages, 193 links, 746
assets.

**Which `DEPLOY.md` you are reading matters here.** On the final branch, §7's divergence block
already reads "✅ RESOLVED — the preview is a build artefact of this repo again" and its deploy
recipe is unblocked. **On `main` it does not**: that copy still carries the "🛑 STOP — the
preview has DIVERGED" block, and following it would send you back to hand-patching artefacts
that no longer need it.

⚠ **But none of that work is on `main`.** `essenta-pro`'s `main` is still `4290f01` (9 Aug) —
three weeks behind, no `lv`/`lt`, the old where-to-buy dataset. Every approved change lives on
unmerged `claude/*` branches, and anyone who builds from `main` gets something older than what is
serving on essentapro.com today.

## To change the site

Change the source repository and redeploy. The recipe is in its `DEPLOY.md` §7 — build against
this origin, confirm `robots.txt` still says `Disallow: /` and every page still carries
`noindex`, then replace this tree.

**`.nojekyll` and `README.md` are the only files here not produced by the build.** A deploy
deletes before it copies, so both have to be restored afterwards or Pages will run the tree
through Jekyll and drop `_next/` — which shows as a fully working page with no styling at all.

## Verifying a deploy

Serve the directory and drive it with a real browser:

```
python3 -m http.server 8099
```

Worth checking every time:

- **Zero page errors.** React error #418 means the markup and the RSC flight payload disagree.
- **Horizontal overflow at 320/360/390/414/640px.** 320 is the tightest — the header logo, the
  menu button and the language select only just fit.
- **Broken images** (`naturalWidth === 0`). Allow generous time: gallery thumbnails are lazy
  and sit inside closed `<details>`, so they report broken until scrolled to and expanded.
- **Scroll-driven reveals actually move.** They use `animation-timeline: view()`, which
  silently freezes if any ancestor becomes a scroll container — `overflow: hidden` does that,
  `overflow: clip` does not. A frozen reveal looks like an empty white panel, not like an error.

⚠ **Full-page screenshots lie here.** The stitching captures each strip at a different scroll
offset, so anything mid-reveal appears half-finished or blank. Take viewport-sized screenshots
after scrolling to a section, or neutralise the animations first.

The bundled Chromium has **no H.264 support**, so `.mp4` playback cannot be verified locally —
every video fails there, including ones that are fine. Check the encode with `ffprobe` instead.

⚠ **It also ships no Georgian or Armenian locale data — which used to fake TWO failures on
`ka` and `hy`, and no longer does.** Worth knowing because the symptom was convincing and the
note that described it stood here for weeks. `Intl.DateTimeFormat('ka', …)` returns
"November 5" in that browser where Node returns "5 ნოემბერი", so the tube-life date the server
rendered and the one the browser hydrated with disagreed; React threw out the server markup,
re-rendered the tree, and the twelve product stills inside the disclosures (`-a.jpg`, `-b.jpg`)
came back with `naturalWidth === 0` — which reads exactly like missing files, and never was.

**It was not only this browser, and that is what finally fixed it.** ICU does not fall back to
English when it lacks a locale; it falls back to the READER'S own. The same gap put a Ukrainian
month on the Armenian page on the owner's machine (2026-09-02), reported as a translation bug.
The source now spells the month from `tubeLife.months` in each locale's `common.json` instead
of asking the browser, so server and client agree in every language whatever locale data is
installed. `src/lib/tubeLife.ts` in the source repo carries the note.

**So a #418 or a broken still on ANY of the seven is real again, `ka` and `hy` included.** The
build in this tree drives clean in that same headless Chromium: zero page errors, zero broken
images, no horizontal overflow at any of the five widths. If you see otherwise, chase it. The
old check, if you ever need to confirm the browser's own gap:

```js
new Intl.DateTimeFormat('ka', { day: 'numeric', month: 'long' }).format(new Date());
// this browser → "November 5"   ·   any real visitor's → "5 ნოემბერი"
```

That line still prints English here. The page no longer cares.
