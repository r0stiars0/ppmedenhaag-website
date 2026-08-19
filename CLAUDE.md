# CLAUDE.md

Context for Claude Code when working in this repository.

## Project overview

**PPME Den Haag** (ppmedenhaag.nl) — a Hugo static site for an Indonesian Islamic community
association based at Masjid Al Hikmah, Den Haag. Content is largely community/mosque
event announcements and news posts.

Three language modes, confirmed in `config/_default/languages.toml`:

- `nl` — Dutch (**default language** — `defaultContentLanguage = "nl"` in
  `config/_default/config.toml` — no URL prefix, e.g. `/event/my-event/`)
- `id` — Indonesian (prefixed, e.g. `/id/event/my-event/`)
- `en` — English (prefixed, e.g. `/en/event/my-event/`)

**Content is NOT nested under `content/event/<lang>/`.** Hugo's multilingual setup here
uses one top-level content directory *per language*, each with its own `event`/`blog`/etc.
subdirectories, wired up via `contentDir` in `config/_default/languages.toml`:

```
content/
├── nederland/   (nl, contentDir for [nl])
│   ├── event/
│   ├── blog/
│   ├── about/, contact/, course/, dakwah/, ibadah/, notice/, ramadhan-1444/, teacher/, author/
├── indonesia/   (id, contentDir for [id])
│   └── (same section layout)
└── english/     (en, contentDir for [en])
    └── (same section layout)
```

**Translations are partial by design, not 1:1 — confirmed by filename comparison.**
`content/nederland/event/` has 68 files, `content/indonesia/event/` has 70,
`content/english/event/` has only 22. Filenames also differ across languages (e.g.
`hadj-oemrah-reunie.md` exists under `nederland/event/` and `indonesia/event/` but has no
`english/event/` counterpart). Blog counts: nl 30, id 29, en 9. Don't treat a missing
`en`/`id` version as a bug to fix automatically; only add a translation if asked.

There is **no `translationKey` field in use anywhere in `content/`** (confirmed via
repo-wide search — the only hit was this file, before this edit). Cross-language linking
between per-language content dirs, if any, is not handled through that mechanism in this
repo; don't assume it exists.

## Language / i18n conventions

- `nl` is the default language: no URL prefix. `id` and `en` are prefixed
  (`/id/...`, `/en/...`).
- Each language has its own content directory (`content/nederland`, `content/indonesia`,
  `content/english`) rather than a shared tree with language subfolders — see above.
- Filenames/slugs are **not** guaranteed identical across languages — verify per case
  rather than assuming a sibling-language file exists at the same path.
- UI strings are translated via `i18n/nl.yaml`, `i18n/id.yaml`, `i18n/en.yaml` (YAML, not
  TOML), each a list of `{ id, translation }` pairs. Do not hardcode language strings in
  templates — use the `i18n` function against these keys.
- Per-language menus live in `config/_default/menus.nl.toml`, `menus.id.toml`,
  `menus.en.toml`.

## Content structure conventions

Sampled several files in `content/nederland/event/` and `content/nederland/blog/` to
confirm the real front matter schema (YAML front matter, `---` delimited):

**`event` front matter fields actually used:**
- `title` (string)
- `publishDate` (page publish date, ISO datetime)
- `date` (the event's own date/time, ISO datetime — distinct from `publishDate`)
- `duration` (free-text string, e.g. `"1h15m"` or `"2 uur"` — not a fixed format)
- `draft` (bool)
- `bg_image` (page header background image path)
- `description` (meta description, often left `""`)
- `image` (event thumbnail/listing image path, under `images/events/...`)
- `location` (free-text string, e.g. `"Al Hikmah moskee"`, `"Online Zoom"`)
- `speaker` (list of objects: `name`, `image`, `designation`) — optional, omitted on some
  events
- `type: "event"`

**`blog` front matter fields actually used:**
- `title`, `draft`, `date`, `description`, `image` (under `images/blog/...`)
- `author` (string)
- `categories` (taxonomy list, e.g. `["Blog"]`)
- `tags` (taxonomy list, free-text, e.g. `["Jadwal","Pengajian","agustus"]`)
- `type: "post"`

No `[taxonomies]` block is defined in `config/_default/config.toml`, so `categories` and
`tags` use Hugo's default taxonomy behavior.

The `archetypes/default.md` archetype is empty (`---\n---`), so `hugo new` will not
scaffold any of the above fields automatically — copy an existing sibling post instead.

Each section also has an `_index.md` with just `title`, `draft`, `bg_image`, and
`description` — no listing-specific params observed beyond that.

Other content sections that exist per language (out of scope unless asked): `about`,
`author`, `contact`, `course`, `dakwah`, `ibadah`, `notice`, `ramadhan-1444`, `teacher`.

## Build & dev commands

No `package.json`, `Makefile`, or CI workflow exists in this repo — Hugo is invoked
directly. Confirmed commands:

- Local dev server (per `README.md`): `hugo server`
- Production build (per `netlify.toml`):
  `hugo --minify --gc -b https://www.ppmedenhaag.nl`
- Netlify deploy-preview build: `hugo --gc --minify --buildFuture -b $DEPLOY_PRIME_URL`
- **Hugo Extended is required** (README: needed to compile SCSS via `assets/scss/`).

## Deploy pipeline

- **Netlify**, configured via `netlify.toml` at repo root. No GitHub Actions
  (`.github/workflows` does not exist) and no `vercel.json`.
- `netlify.toml` pins `HUGO_VERSION = "0.87.0"` and sets `TZ = "CET"` in
  `[build.environment]`.
- Publish directory: `public`.
- Security headers are set in `netlify.toml` (`X-Frame-Options`, `X-XSS-Protection`,
  `Referrer-Policy`, `Strict-Transport-Security`) plus long-cache `cache-control` headers
  for `*.js`, `*.woff`, `*.css`.
- README badge links to `app.netlify.com/sites/ppmedenhaag/deploys`.

## Hugo version & config flags

- **Hugo 0.87.0**, confirmed from `netlify.toml`'s `HUGO_VERSION` — also matches the
  generator meta tag on the live site. There is no `go.mod` (not using Hugo Modules) and
  no `.tool-versions`.
- `config/_default/config.toml` key settings:
  - `baseURL = "https://www.ppmedenhaag.nl"`, `title = "PPME Den Haag"`
  - `defaultContentLanguage = "nl"`
  - `paginate = "6"`, `summaryLength = "10"`
  - `pluralizelisttitles = false`
  - `[markup.goldmark.renderer] unsafe = true` (raw HTML in Markdown is allowed —
    content files do use raw `<table>`/`<tr>` HTML, e.g. prayer-time schedules in blog
    posts)
  - No `[imaging]` or `[minify]` config blocks are set — minification comes only from the
    `--minify` CLI flag in the Netlify build command, not from `hugo.toml`/`config.toml`.
  - No `[taxonomies]` block — defaults apply.
- `config/_default/languages.toml` defines `[nl]`, `[id]`, `[en]` each with
  `languageName`, `languageCode`, `contentDir`, `weight`, `home` (see content structure
  section above for the `contentDir` mapping).

## Theme

**Not a submodule, not an npm package, not in `/themes`.** There is no `themes/`
directory and no `.gitmodules` file. Per `README.md`, the site was originally built on
the [Educenter Hugo theme](https://github.com/themefisher/educenter-hugo) but the theme
has been **vendored directly into the repo root** — `config.toml` even has a commented-out
`#theme = "educenter-hugo-not-used"` line confirming it's intentionally not loaded as a
theme.

- Layouts: `layouts/` at repo root — `_default/` (`baseof.html`, `list.html`,
  `single.html`, plus type-specific `post.html`, `course.html`, `event.html`,
  `notice.html`, `teacher.html`), `partials/`, `shortcodes/`, and section-specific
  directories `event/` (`list.html`, `single.html`), `about/`, `contact/`, `course/`,
  `dakwah/`, `ibadah/`, `notice/`, `ramadhan-1444/`, `teacher/`, `author/`, `pages/`.
  Note: blog posts have `type: "post"` (not `"blog"`) and have no `layouts/blog/`
  directory — they render via `layouts/_default/post.html` and `list.html` instead.
- Assets (SCSS/JS pipeline via Hugo Pipes): `assets/scss/` (`main.scss`,
  `_variables.scss`, `_mixins.scss`, `_typography.scss`, `_buttons.scss`,
  `templates/*.scss`) and `assets/js/script.js`.
- Static/vendored third-party plugins: `static/plugins/` (`bootstrap`, `slick`,
  `animate`, `venobox`, `themify-icons`, `filterizr`, `jQuery`, `google-map`), referenced
  via `[[params.plugins.css]]` / `[[params.plugins.js]]` in `config.toml`.
- Other `static/` contents: `data`, `docs`, `fonts`, `forms`, `images`.

## Tech stack & style preferences

- Any JS/TS in this repo: **TypeScript preferred**, fall back to JavaScript, avoid Python
  unless there's no alternative for a specific tooling task. (Note: existing JS in this
  repo — `assets/js/script.js`, vendored plugins — is plain JS; this preference applies to
  new tooling, not a mandate to convert existing vendored code.)
- Any interactive UI components: **React + Tailwind CSS**. (Note: the current site itself
  uses plain Hugo templates + Bootstrap/jQuery-based vendored plugins, not React/Tailwind —
  this preference applies to any new tooling/components, not a description of the existing
  stack.)
- Currency in content/UI: **EUR** (confirmed live: `iban` param in `config.toml` uses
  `IBAN NL26 INGB...`, an ideal/Sumatera donation link is also in EUR context).
  Timezone for dates/times: **CET** (confirmed: `TZ = "CET"` in `netlify.toml`, and event
  front matter uses `+01:00`/`+02:00` offsets).

## Things to avoid

- Don't restructure the per-language `content/nederland|indonesia|english/` layout, or the
  `event`/`blog` directories within them, without confirming first.
- Don't introduce a new language folder without explicit instruction.
- Don't assume a `translationKey` mechanism exists — it doesn't, in this repo.
- Don't assume filenames/slugs match 1:1 across the three language content directories —
  verify per case.

## Confirmed from the live site (ppmedenhaag.nl)

- Language set: `nl` (default) / `id` / `en` — confirmed via switcher and live `/en/`
  URLs, and matches `config/_default/languages.toml`.
- Hugo generator meta tag reports `0.87.0`, matching `HUGO_VERSION` pinned in
  `netlify.toml`.
- Site also has non-`event`/`blog` sections (Dakwah, Ibadah, About, Contact, Course,
  Notice, Teacher, Author, and a `ramadhan-1444` section) — out of scope here per current
  instructions, noted so Claude doesn't assume the whole site is just `event`/`blog`.

## Open items to fill in as the project firms up

- [ ] No lint/test commands were found anywhere in the repo (no `package.json`,
      `Makefile`, or CI config) — confirm with the user whether any exist outside the
      repo (e.g. Netlify build-time checks beyond the plain `hugo --minify` build).
