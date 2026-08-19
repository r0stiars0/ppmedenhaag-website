---
name: new-event-post
description: >
  Use when the user wants to publish, add, or create a new event on the PPME Den Haag
  Hugo site from a short input — a few paragraphs of raw text plus an image. Triggers on
  phrases like "publish a new event", "add this event to the site", "create an event
  post for X", or when the user pastes event details (what/when/where) and an image and
  asks to get it live. Not for editing existing events or for blog posts (see separate
  blog-post skill if one exists).
---

# New Event Post

Turn a short, informal input (paragraph(s) of event info + one image) into a properly
structured, front-matter-complete Hugo content file in the right per-language content
directory, with the image placed correctly and (for upcoming, featured events) the
homepage CTA updated — then let the user preview before committing.

## Step 1 — Gather the raw input

Expect from the user:
- Free-text description of the event (what, when, where — may be informal or incomplete)
- One image (flyer/photo)
- Optionally: which language(s) to publish in (default: ask if not stated — see Step 3)

If the date, time, or location isn't clearly stated in the text, ask for it before
proceeding — don't guess these for a real public event listing.

## Step 2 — Extract structured fields

From the raw paragraph(s), pull out:
- `title` — short, descriptive event name
- `date` — the event's own date/time (see "Confirmed front matter schema" below for
  format and timezone offset)
- `location` — venue name (free text — no fixed schema; e.g. `"Masjid Al Hikmah"`,
  `"Moskee Al-Hikmah, Den Haag"`, `"Online Zoom"`)
- `description` — a 1–2 sentence meta description. Many existing posts leave this `""`,
  but recent posts (2026) fill it in — prefer writing a real one from the event text
  unless the user says not to bother.
- The full body text, lightly cleaned up (fix obvious typos, keep the user's voice/tone,
  including emoji-bullet summary lines and Assalamu'alaikum/Wassalamu'alaikum
  opening/closing greetings that nearly every post uses — don't rewrite their message)

## Step 3 — Determine the slug and languages

- Generate a URL-safe slug from the title (lowercase, hyphenated, no special characters,
  e.g. `nongkrong-sessie-2`, `diskusi-produk-makanan-halal`). **Reuse this exact slug as
  the filename across every language version** — confirmed convention on this site (e.g.
  `iduladha-1447.md` exists in `content/nederland/event/`, `content/indonesia/event/`,
  and `content/english/event/`).
  - Note: a handful of legacy posts are named generically (`event-1.md` … `event-13.md`).
    Don't follow that pattern for new posts — always use a descriptive kebab-case slug.
- If the user didn't specify which language(s) to publish in, ask — don't assume all
  three (`nl`/`id`/`en`) are wanted. This site's translations are intentionally partial;
  many events exist only in `nl`, and even recent events (e.g. `nongkrong-sessie-2`) were
  published in `nl` + `en` but skipped `id`.
- If asked to translate: translate the content faithfully, keep the same slug, image, and
  event `date`/`duration` (translate `duration`'s text, e.g. `"1,5 uur"` → `"1.5 hours"`),
  and keep tone appropriate for a community/mosque announcement.

## Step 4 — Place the image

- Save the image as a static file at `static/images/events/<filename>`. This is the
  **only** location used across every event post sampled — there is no page-bundle image
  pattern here (see Step 5 on why this is a flat-file structure, not a bundle).
- Reference it in front matter as `image: "images/events/<filename>"` — **no** `static/`
  prefix (Hugo serves `static/` at the site root).
- Naming: prefer `<slug>.webp`. If the event may recur (e.g. a series of one-off
  Nongkrong sessions, or a talk that could repeat), the current convention appends the
  event date: `<slug>-<YYYYMMDD>.webp` (confirmed on the most recent post,
  `nongkrong-sessie-20260822.webp` for `nongkrong-sessie-2.md`).
- **`.webp` is the preferred format going forward — a convention, not a hard
  requirement.** Convert to `.webp` when practical (older posts have inconsistent
  extensions — `.jpg`/`.png`/`.jpeg` — so this isn't enforced site-wide). If conversion
  isn't feasible in the moment, it's fine to keep the source format rather than block on
  it — just don't propagate `.jpg`/`.png` as if it were the current standard.
- The same image file is shared across all language versions of a post — don't duplicate
  or rename per language.

## Step 5 — Write the content file(s)

**Important correction:** this is a flat per-language content directory, **not** a
Hugo page bundle. For each language being published, create a single file at:

```
content/nederland/event/<slug>.md    (nl — default language, no URL prefix)
content/indonesia/event/<slug>.md    (id)
content/english/event/<slug>.md      (en)
```

There is no `content/event/<lang>/` path and no `<slug>/index.md` bundle directory —
every existing event post in this repo is a single flat `.md` file directly inside the
per-language `event/` folder.

### Confirmed front matter schema (sampled ~15 posts, incl. the most recent, 2026-08)

```yaml
---
title: "Event Title"
# Schedule page publish date
publishDate: "2026-08-19T00:00:00+02:00"
# event date
date: "2026-08-22T19:30:00+02:00"
duration: "1,5 uur"
# post save as draft
draft: false
# page title background image
bg_image: "images/backgrounds/page-title.jpg"
# meta description
description: "One or two sentence summary for meta/SEO."
# Event image
image: "images/events/<slug>.webp"
# location
location: "Masjid Al Hikmah"

# type
type: "event"

speaker:
  # speaker loop
  - name : "Full Name"
    image : "images/event-speakers/speaker-1.svg"
    designation : "Role, e.g. Khotib / Host / Researcher"
---
```

Notes on each field, from what's actually observed:
- `publishDate` — set to **today's date** (when the post is being added to the site), at
  `T00:00:00`, not the event date. Confirmed on the newest post (published 2026-08-19 for
  an event on 2026-08-22).
- `date` — the event's actual date/time. Use the correct CET/CEST offset for that
  calendar date (`+01:00` roughly Nov–Mar, `+02:00` roughly Apr–Oct) — a couple of older
  posts got this wrong (e.g. a January `publishDate` stamped `+02:00`); don't copy a
  mismatched offset from a sampled file, compute it from the actual date.
- `duration` — free text, no fixed format (examples in the wild: `"1h15m"`, `"2 uur"`,
  `"1,5 uur"`, `"~5h"`, `"2 jam"`, `"..."` as a placeholder when unknown). Match the
  post's language.
- `bg_image` — always the same constant, `"images/backgrounds/page-title.jpg"`, across
  every post. Don't change it.
- `description` — inconsistent across history: many older/simpler posts leave it `""`.
  Prefer writing a real 1–2 sentence description (recent posts do this) unless told
  otherwise.
- `speaker` — optional list, omitted on most posts. When present, `image` is **always**
  the same generic placeholder `images/event-speakers/speaker-1.svg` regardless of who
  the speaker is — there's no per-speaker photo asset in use, don't try to source one.
- `type: "event"` — always present, always exactly this value.
- Inline `#` comments on each field (as shown above) appear in most but not all posts
  (e.g. `diskusi-produk-makanan-halal.md` has none). Including them is fine and matches
  the majority convention; omitting them is also seen in the wild and won't break
  anything.
- The `_index.md` file in each language's `event/` folder (section listing page) only has
  `title`, `draft`, `bg_image`, `description` — do not add new fields there, and don't
  touch it for a single new event post.

### Body content conventions

- Opens with `Assalamu'alaikum warohmatullahi wa barakatuh` (spelling varies slightly),
  closes with `Wassalamu'alaykum wa rahmatullahi wa barakaatuh`.
- A dense emoji-prefixed summary block is common right before any RSVP link, e.g.:
  `📅 Zaterdag 22 augustus 2026 | 9 Rabï' al-Awwal 1448 AH<br/>` /
  `📍 Moskee Al-Hikmah, Den Haag<br/>` / `🎟️ Deelname is gratis...<br/>` — each line ends
  in `<br/>`.
- RSVP/registration buttons use this exact HTML snippet pattern (raw HTML is allowed —
  `unsafe = true` in the site's goldmark config):
  ```html
  <div class="row py-1">
        <div class="col-12 text-center">
        <a href="<form-url>" type="button" class="btn-sm btn-primary">Aanmelden Google Form</a>
        </div>
  </div>
  ```
  (translate the button label per language, e.g. `"Register via Google Form"` in en).
- Schedule/program tables (common for multi-session events like Ramadan programs) use raw
  HTML `<table>`/`<tr>`/`<th>` markup, not Markdown tables — copy the existing pattern
  from a similar recent post if the new event needs one, rather than inventing one.

## Step 6 — Update the homepage CTA (upcoming events only)

Each language's homepage has exactly **one** featured call-to-action banner, driven by
the `cta:` block in `data/nl/homepage.yml`, `data/id/homepage.yml`, `data/en/homepage.yml`
(rendered by `layouts/index.html`, the `<!-- cta -->` section). This is the only place a
new event becomes visible on the homepage — creating the content file alone does not
surface it there, it only shows up in the `event` listing section
(`homepage.event`, which pulls from `content/**/event` automatically) and the
`/event/` archive.

**If the new event is upcoming (its `date` is in the future) and is meant to be the
site's featured/promoted event, ask the user whether it should replace the current
homepage CTA** — there's only one slot, so promoting the new event means demoting
whatever is currently featured (e.g. a fundraiser, umrah trip, or another event). Don't
overwrite it silently.

If confirmed, for **each language the event was actually published in** (per Step 3 —
don't point a CTA at a language that has no content file for that slug, see the caution
below), edit that language's `cta:` block:

```yaml
cta:
  enable : true
  subtitle : "<day + full date, in that language, e.g. \"Zaterdag 22 augustus 2026\">"
  title : "<event title, matching that language's front matter title>"
  button:
    enable : true
    label : "<Lees Meer (nl) / Selengkapnya (id) / Read More (en)>"
    link : "event/<slug>/"
  youtube:
    enable : false
    title : ""
    id : "-1HiLtHLAGk"
  img:
    enable: true
    src: "/images/events/<same image filename used in the event's front matter>"
    desc: "<short alt text describing the image, in that language>"
```

- `button.link` is relative and unprefixed (`"event/<slug>/"`) even for `id`/`en` — the
  language prefix is added by the template (`absLangURL`), don't include `/id/` or `/en/`
  yourself.
- `img.src` **does** need the leading `/` (unlike front matter's `image` field) since it's
  used directly in an `<img src>`, not run through Hugo's image processing.
- **Don't delete the previous `cta:` block** — comment out every line of it with `#` and
  leave a one-line note above stating what it was and when to restore it, matching the
  existing convention (Dutch: `# Vorige CTA - <name> (terugzetten na <date>)`, Indonesian:
  `# CTA sebelumnya - <name> (aktifkan kembali setelah <date>)`, English:
  `# Previous CTA - <name> (restore after <date>)`). This preserves it for easy revert
  once the new event has passed.
- **Caution — verified inconsistency in this repo:** as of this writing, `data/id/homepage.yml`'s
  CTA links to `event/nongkrong-sessie-2/`, but no `content/indonesia/event/nongkrong-sessie-2.md`
  file exists — that's a dead link in the `id` homepage today. Don't repeat this mistake:
  always confirm the target content file exists in that language before pointing a CTA at
  it, and flag it to the user if you find the CTA and the content have already drifted
  apart for the event you're replacing.

## Step 7 — Preview before publishing

- Run the site's dev server (see CLAUDE.md for the exact command) and report the local
  preview URL(s) for each language version created (`nl`: `/event/<slug>/`, `id`:
  `/id/event/<slug>/`, `en`: `/en/event/<slug>/`), plus the homepage (`/`) if the CTA was
  updated.
- Do not commit or push unless the user explicitly asks you to — show the diff /
  new files first so they can review title, date, location, image placement, and any
  `homepage.yml` CTA change.

## Notes

- If `CLAUDE.md` in this repo has since been updated with the confirmed front-matter
  schema, build commands, or image conventions, defer to that over the guesses in this
  skill — update this file to match once confirmed, so future runs don't have to
  re-derive it.
