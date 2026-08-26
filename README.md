<div align="center">
  <img src="public/logo.png" alt="Logo" width="150"/>
  <h1>Harel Shemesh — Artist Site & Content Platform</h1>
  <h3>Commercial Project Showcase</h3>
  <p>A Next.js 16 site for a working artist: a gallery of original canvas paintings, handmade terrariums and upcycled pieces, a wall preview that hangs a painting in a room at its true size in centimetres, a CMS the artist runs himself, and an enquiry inbox that reaches him on WhatsApp.</p>

  <a href="https://harelshemesh.co.il">
    <img src="https://img.shields.io/badge/Live%20Site-harelshemesh.co.il-5c6b50?style=for-the-badge&logo=globe&logoColor=white" alt="Live Site">
  </a>

  <br />

  </div>

---

> **ℹ️ Note:** This repository is a **technical showcase** for a commercial project. The source is closed for client privacy and IP reasons; this README documents the architecture, the engineering decisions and the features in the production build.

---

<div align="center">

### Tech Stack

![Next.js](https://img.shields.io/badge/Next.js_16-000000?style=flat-square&logo=nextdotjs&logoColor=white)
![React](https://img.shields.io/badge/React_19-61DAFB?style=flat-square&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript_strict-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_v4-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-3FCF8E?style=flat-square&logo=supabase&logoColor=white)
![Drizzle](https://img.shields.io/badge/Drizzle_ORM-C5F74F?style=flat-square&logo=drizzle&logoColor=black)
![Sentry](https://img.shields.io/badge/Sentry-362D59?style=flat-square&logo=sentry&logoColor=white)
![Vercel](https://img.shields.io/badge/Vercel-000000?style=flat-square&logo=vercel&logoColor=white)

</div>

---

## About

Harel works in three media — canvas paintings, terrariums, and pieces built from
discarded objects — and needed a site that reads as a window into that rather than as a
brochure. This is the second version. The first was a static React/Vite single-page site;
this one is a full application, and the reason is the two problems the static site could
not solve: a buyer cannot picture a canvas on their own wall from a thumbnail, and an
artist who has to ask a developer for every text change stops updating the site within
two months.

Built end-to-end as the sole engineer, on a private full-stack template of mine —
so the module system, the audit engine and the database rules below are platform work
this project inherited, while the wall preview, the video postmortems and the media
migration are specific to it.

---

## Wall Preview — the feature the rest is built around

Selling a painting online stalls on one question: *how big is it, really, and how will it
look in my room?* Dimensions in centimetres do not answer it.

So the site hangs the piece on a wall. A room photograph is calibrated once — the wall's
real width in centimetres against its width in pixels — which gives a `pixelsPerCm` for
that scene, and from then on a 70×80cm canvas renders at 70×80. A human figure at true
height stands beside it for scale. Frames, rooms and lighting are selectable, and the
result exports as a single PNG through the Web Share API so a buyer can send it to
whoever they decide these things with.

**What makes it look real is arithmetic, not a model.** The commercial versions of this
feature run a generative model on a server and still read as a sticker. Here it is four
cheap tricks: true-centimetre scale, a frame with a lit bevel for depth, a cast shadow,
and — the one that does most of the work — a second blurred copy of the room photograph
composited over the artwork, so it picks up that room's own light and colour cast instead
of sitting on top of it.

The interactive preview is percentage-based DOM and CSS; only the save button builds an
actual `<canvas>`. Both paths import the **same `placeOnWall()` geometry function**, so
the two renderers cannot disagree about where the painting hangs — what each restates is
only the paint, never the placement. When a canvas is larger than a room's clear wall, it
is scaled down and the UI says so: of the three available answers, silently lying about
the size and refusing to render are the two worse ones.

---

## Features

- **Gallery in two layouts** — a captioned four-column catalogue and a masonry wall of
  uncropped photographs, both driven from the same component. Artwork images and video
  clips share every slot.
- **Lightbox** — built on Radix `Dialog`, so focus is trapped and the page cannot scroll
  behind it. Pinch-zoom and pan, swipe navigation, and arrow keys mapped for Hebrew:
  `ArrowLeft` advances, because the page reads right to left.
- **The artist runs the content** — eleven content areas (hero, about, each of the three
  media, FAQ, contact, footer, legal and more) are edited from an admin panel with no
  code and no developer in the loop. Content lives in the database, not in files.
- **Enquiries reach a phone, not an inbox** — the contact form carries its UTM parameters
  and referring page, files the lead in an admin inbox, and sends the artist a WhatsApp
  message. The email stack it replaced was removed outright: the client reads WhatsApp.
- **Analytics with nothing to leak** — page views, unique visitors and conversions,
  where a visitor is a salted hash that rotates daily. No IP address and no cookie is
  ever stored. GA4 and Meta Pixel load only behind a consent reader.
- **Accessibility** — a floating menu on every page with greyscale, high contrast, text
  resizing, link highlighting and reduced motion, built against Israeli standard
  ת״י 5568 at AA (WCAG 2.0).
- **Installable** — a Serwist service worker, built in a second pass after `next build`
  so its precache manifest names the finished build's hashed assets.

---

## Engineering Decisions Worth Highlighting

### The hero video played everywhere except the client's phone

`<video autoPlay>` with an `onCanPlay` handler never fired on iOS. Safari begins resource
selection while parsing the HTML — before React hydrates and attaches the listener — so
the event the code waited for had already passed. The tell was that the *previous* Vite
site played the byte-identical file: it was client-rendered, so its listener was attached
before the element existed.

The fix — `preload="none"`, no `autoplay` attribute, and an imperative `play()` driven by
an `IntersectionObserver` — then shipped with three holes of its own, which the client
found on his own iPhone two days later: a ternary that treated the poster and a time
fragment as alternatives, a `networkState` guard that could never be true, and a
synchronous `paused` flip that locked out every retry. A stall watchdog closed it.

Worth stating plainly: the iOS Simulator never reproduced any of this. It took real
hardware.

### `<source media="…">` had not worked since 2014

Two encodes of each clip, desktop and phone, were selected with `media` attributes on
`<source>`. That mechanism left the HTML standard over a decade ago, and every
non-Safari desktop browser took the first source in the list and quietly played the 720p
phone file. It is now a mechanically enforced rule (`no-source-media-attribute`) plus a
hook that picks the encode, because a convention nothing checks is a convention that
comes back.

### The clever masonry algorithm was deleted, not maintained

The first version shipped a custom JavaScript column-distribution algorithm — about
thirty-five lines plus a `resize` subscription — to work around a WebKit RTL bug in
`column-count` and to preserve reading order across columns. WebKit fixed the bug in
26.5, and a redesign that gave every piece a caption made reading order moot. Plain CSS
grid and columns replaced the lot, taking the reflow it caused with it.

### A guard test for every rule, because a green audit lied once

`audit:standards` runs 52 mechanical rules across concurrency, database and RLS,
caching, RTL and project structure — and each rule has a required test case. That
requirement exists because an audit once reported clean while a page ran eight sequential
database queries: the rule only ever looked for `Promise.all`. The escape hatch is an
inline comment naming the rule and giving a reason; there is deliberately no way to
disable a rule for a whole file.

### The gate names what it does not cover

`verify` runs lint, format, types, a compile-only build, the Vitest suites, and the
mechanical audits. It deliberately excludes the checks that need a running server — and
that omission has a measured price: on one occasion it was 190 contrast failures across
every public route, plus a page serving another page's meta description, while `verify`
stayed green throughout. Those checks exist (`audit:served` starts a real headless Chrome
over CDP and tears it down again); they are run on demand, and the gap is written down
rather than discovered twice.

### `build:compile` is in the gate because its absence hid a broken build

The template enabled React Compiler without its Babel plugin. Every fresh checkout failed
`npm run build` — for the template's entire life — while `verify` stayed green, because
the pipeline assumed Vercel's own build step covered it. True for a deployed app; false
for a template that is never deployed.

### Migration order is declared, not inferred

Migrations are hand-written and forward-only; the SQL is the authority and the Drizzle
schema is types only. The apply order lives in the migration script rather than being
read off filenames, because lexical order across core and module directories is not
dependency order. Twenty-one migrations, each in its own transaction, recorded in
`schema_migrations` so a re-run is a no-op — and every `CREATE TABLE` enables row-level
security in the same migration that creates it.

### Uploads are one path, and it does the boring work

Every admin upload goes through a single function: images are converted to WebP in a web
worker before they leave the browser, dimensions are read with `createImageBitmap` so
nobody types a pixel size into a form, and the object key is a random UUID rather than
the filename — Hebrew filenames do not survive percent-encoding through storage and a CDN
intact. Video posters are captured from the clip's own first frame, added after a
hand-made poster shipped visibly colour-graded differently from the video behind it.

---

## Quality Gates

| Gate | What it enforces |
| :--- | :--- |
| `check` | ESLint, Prettier, `tsc --noEmit` (TypeScript strict) |
| `build:compile` | The production build compiles — in the gate for the reason above |
| `test` | Vitest across two projects: pure Node, and jsdom for DOM behaviour |
| `audit:standards` | 52 mechanical rules, each with a required test case |
| `audit:i18n` | A missing message key fails the build |
| `audit:served` | Real headless Chrome over CDP: CSP violations, hydration errors, WCAG contrast on the rendered DOM, canonical/OG/JSON-LD, heading order, landmarks and accessible names |

Sentry is wired with a `beforeSend` scrubber that redacts anything matching
password/token/cookie/card at any depth before an event leaves the process, and a
`tunnelRoute` so an ad-blocker cannot silently swallow every report.

---

<div align="center">
  <small>© 2025 All Rights Reserved to Harel Shemesh (Content) & Sagi Menahem (Code).</small>
</div>

<div align="center">

**Built by Sagi Menahem**

[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/sagi-menahem)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/sagi-menahem/)
[![Portfolio](https://img.shields.io/badge/Portfolio-0D2440?style=flat-square&logo=googlechrome&logoColor=white)](https://sagimenahem.tech)

</div>
