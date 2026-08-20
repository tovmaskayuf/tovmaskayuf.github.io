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

⚠ **It also ships no Georgian or Armenian locale data, and that fakes TWO failures on `ka` and
`hy` — a React #418 AND twelve broken gallery images.** `Intl.DateTimeFormat('ka', …)` returns
"November 5" in that browser where Node returns "5 ნოემბერი", so the tube-life date the server
rendered and the one it hydrates with disagree. React then throws out the server markup and
re-renders the tree, and the twelve product stills inside the disclosures (`-a.jpg`, `-b.jpg`)
come back with `naturalWidth === 0` — which reads exactly like missing files. Nothing is
missing: no request 404s, and the same files load on the other five languages.

**Confirm it before chasing either symptom**, because the fix for a real one is nowhere near
here:

```js
new Intl.DateTimeFormat('ka', { day: 'numeric', month: 'long' }).format(new Date());
// this browser → "November 5"   ·   any real visitor's → "5 ნოემბერი"
```

Hand that browser the month names it lacks — patch `Intl.DateTimeFormat` in an init script so
`ka` formats the way Node did — and both symptoms vanish together: no page error, no broken
image. `uk`, `en`, `pl`, `lv` and `lt` are unaffected, so a #418 or a broken still THERE is
real and worth chasing.
