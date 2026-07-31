# Suited — "Signal" scroll animation: Webflow embed handoff

> **Provenance.** This repository is a vendored copy of `h-emiliia/suited-embed`, taken at
> upstream commit `66ea8dd82729458218e523ed262aa248ff4d24b0` on 2026-07-30. Full upstream
> commit history and tags (`v1`–`v12`) are preserved as-is; original authorship is retained
> in the git metadata. This copy is the source of truth for what wellsuited.com loads in
> production. Upstream is not a dependency and is not tracked as a remote.

A scroll-scrubbed Chladni particle animation with 3 morphing states ("Firm / Industry /
Individual signal"). It's **one JS file** plus a small DOM structure built in Webflow.
All text stays as **native, editable Webflow elements** — the script only injects a
transparent `<canvas>`, scrubs the morph on scroll, toggles an `is-active` class on the
current label, and crossfades the descriptions.

## Everything you need
- **Embed kit (code + this doc):** https://github.com/samfogarty5/suited-embed
- **Script (CDN, production-ready):**
  `https://cdn.jsdelivr.net/gh/samfogarty5/suited-embed@v13/embed.js`

## Add the script

In Webflow → **Page Settings → Custom Code → Before `</body>`**:

```html
<script src="https://cdn.jsdelivr.net/gh/samfogarty5/suited-embed@v13/embed.js"
        integrity="sha384-bRcbEkLO/vZZg0c5ak4h3Fr8sh9nq7Nn58m2VcPuYMbGFxuuVabnNTRbBt8o1JAo"
        crossorigin="anonymous"></script>
```

> **No `defer`.** On the live page this tag sits between the Lenis CDN script and an inline
> `DOMContentLoaded` initializer. Adding `defer` would push execution past that initializer
> and change boot order. Keep the tag bare and keep its position.

> Pinned to `@v13` (a git tag), never `@main` — a branch ref lets any push to this repo go
> live automatically, which is exactly the exposure this copy exists to remove. The
> `integrity` hash means the browser refuses the file if its bytes ever change.
>
> To ship a change: push a new tag, recompute the hash with
> `shasum -b -a 384 embed.js | cut -d' ' -f1 | xxd -r -p | base64`, and update both the tag
> and the hash in the Webflow embed.

## 1. Structure to build in the Designer

```
Section / Div                       data-chladni = wrapper      (DO NOT set a height)
└─ Div  "hero"                      position: sticky; top: 0; height: 100vh; overflow: hidden
   ├─ Div  "canvas mount"           data-chladni = canvas       (absolute, centered)
   ├─ Div  "labels"
   │  ├─ Text "Firm signal"         data-chladni = label  data-m=6.5  data-n=9    data-a=-1.2   data-b=1.7
   │  ├─ Text "Industry signal"     data-chladni = label  data-m=6.1  data-n=4    data-a=-2     data-b=0.45
   │  └─ Text "Individual signal"   data-chladni = label  data-m=4.9  data-n=8.1  data-a=-1.85  data-b=-2
   └─ Div  "descriptions"
      ├─ Text  data-chladni = desc  "What the leading firms are doing — hiring, strategy, and the moves that define the market."
      ├─ Text  data-chladni = desc  "How professionals succeed across law and finance."
      └─ Text  data-chladni = desc  "Your own trajectory — benchmarked, contextualized, and ready to act on."
```

- Custom attributes: **Element Settings → Custom attributes**.
- Labels and descriptions are matched **by order**. Add a label+desc pair to add a section.
- The description copy above is placeholder — swap for final copy any time.

## 2. Required styles

- **wrapper** — `position: relative`; **do not set a height** (the script sets it to
  `100vh + 100vh` per extra section = 300vh here).
- **hero** — `position: sticky; top: 0; height: 100vh; overflow: hidden`.
- **canvas mount** — `position: absolute`, centered. The script creates + sizes the `<canvas>`.
- **descriptions** — stack them: container `position: relative` + fixed/min height, each
  desc `position: absolute`. The script crossfades opacity; it does not lay them out.
- **is-active** — the script adds class `is-active` to the active label; style it (white
  text, show the arrow) with `transition: color .4s` for a soft swap.

## 3. Mobile

On the Mobile breakpoint, stack vertically — **labels centered above**, **descriptions
centered below**:
- labels: `top: 11vh`, full width, `text-align: center`, no transform.
- descriptions: `bottom: 11vh`, full width, centered, `min-height: ~84px` (keep each desc
  `position: absolute` so the crossfade still works).
- optionally shrink the canvas a touch.

These are breakpoint overrides of the desktop positioning — no code needed.
`example.html` in the repo demonstrates the exact rules.

## 4. Notes / gotchas

- **Transparent canvas** — set the section's background (dark) yourself; the plate has a
  soft radial edge fade and blends into any color.
- **Patterns** are read only from `data-m/n/a/b` — retune a section by editing attributes.
- **Particle look / scroll feel / colour** live in the `SIM` / `SCROLL` config at the top
  of `embed.js` (particle colour is `rgba(190,190,184)`). Changing these means editing
  `embed.js` and pushing a new tag.
- **Reduced motion** — with `prefers-reduced-motion: reduce`, the morph snaps between
  states instead of scrubbing.
- **Lenis** — the site uses Lenis for smooth scrolling, which works fine with CSS sticky.
  (CSS sticky can't work inside GSAP's ScrollSmoother's transformed scroller). 
- The script is inert if the `data-chladni` elements are absent, so it's safe site-wide.
