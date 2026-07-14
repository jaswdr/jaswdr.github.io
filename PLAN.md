# Plan: Improve `jaswdr.github.io`

## Current state

This repository is effectively empty. It has a single commit from April 2020 containing:

| File | Purpose |
|------|---------|
| `LICENSE` | Apache 2.0 |
| `README.md` | One-line title |
| `.gitignore` | Jekyll build artifacts (`_site/`, `.sass-cache/`, etc.) |

There is no application code, no site layout, no CI, and no content beyond the stub README. GitHub Pages is serving the default Jekyll README render at https://jaswdr.github.io/.

The real personal site already exists elsewhere:

| Asset | Location | Role |
|-------|----------|------|
| Live site | https://jaswdr.dev/ | Canonical personal/CV site (Cloudflare) |
| Site source | [`jaswdr/blog`](https://github.com/jaswdr/blog) | Hugo single-page CV, YAML-driven, Docker/nginx |
| PDF CV | [`jaswdr/cv`](https://github.com/jaswdr/cv) | Makefile-built PDF on `gh-pages` |
| Profile README | [`jaswdr/jaswdr`](https://github.com/jaswdr/jaswdr) | GitHub profile README |

So “improving this codebase” is less about refactoring existing application code and more about deciding what this User Pages repo should be, then executing that decision cleanly.

---

## Goals

1. Stop presenting an abandoned default page on `*.github.io`.
2. Align hosting and DNS responsibility so there is one canonical home (`jaswdr.dev`).
3. Keep content maintenance simple (YAML / Markdown) without duplicating CV data across repos.
4. Optionally grow from a CV landing page into a stronger engineering presence (projects, writing).

---

## Recommended direction

**Make `jaswdr.github.io` a thin redirect + pointer repo, and keep product work in `jaswdr/blog`.**

Rationale:

- The Hugo CV site in `blog` is already solid: data-driven content, JSON-LD, `llms.txt` / `cv.txt` / `cv.json`, CI validation, Docker/nginx packaging, theme toggle, Open Graph image generation.
- Rebuilding the same site here as Jekyll would duplicate maintenance.
- User Pages repos (`username.github.io`) still attract traffic and SEO; a permanent redirect protects reputation and avoids a stale second copy of the CV.

Alternative considered and deferred: migrate the Hugo site into this repo and point `jaswdr.dev` at GitHub Pages. That consolidates naming but disrupts a working Cloudflare + Docker deploy path for little gain.

---

## Work packages

### 1. Immediate hygiene for this repo (low risk)

Do these first; they improve the public face of https://jaswdr.github.io/ without changing DNS.

- Rewrite `README.md` to state purpose (“redirect / portal to jaswdr.dev”), link the live site, CV formats, GitHub, and LinkedIn.
- Replace Jekyll-oriented `.gitignore` with something appropriate for a static redirect (or keep minimal ignores only).
- Add a real `index.html` with:
  - Meta refresh and JS `location.replace` to `https://jaswdr.dev/`
  - `<link rel="canonical">` pointing at `jaswdr.dev`
  - A no-JS fallback link and short identity blurb
- Add `CNAME` only if this repo becomes the host for `jaswdr.dev` (skip while Cloudflare owns the domain).
- Optionally disable unused GitHub Pages “Improve this page” theme chrome by using a plain static site (no Jekyll theme) via an empty `_config.yml` or by committing `index.html` as the sole content.

**Risk:** Low. Redirect pages are static and easy to revert.

### 2. Decide DNS / Pages ownership (decision gate)

Pick one and document it in this README:

| Option | Behavior | When to choose |
|--------|----------|----------------|
| **A. Keep Cloudflare → `blog`** (recommended) | `jaswdr.dev` unchanged; `github.io` redirects to it | Status quo works |
| **B. Point `jaswdr.dev` → this repo** | Migrate Hugo site (or built artifacts) here | Want everything under `*.github.io` naming |
| **C. Archive / soft-deprecate** | README + redirect only; archive later | Zero desire to maintain this repo |

Do not implement B until A’s redirect and README are in place, and until deploy for `blog` is reproducible in CI/CD docs.

### 3. Fix seams across the personal-site ecosystem

These bugs and gaps live mostly in sibling repos but affect this plan’s outcome:

**`jaswdr/blog`**

- README clone URL still says `github.com/jaswdr/website` — should be `jaswdr/blog` (or rename the repo to `website` for clarity).
- Repo is named `blog` but is a CV landing page with no posts; rename or add a real `/blog` section to match the name and homepage marketing.
- No sitemap / RSS today (`disableKinds` turns them off). Fine for a one-pager; revisit if posts are added.
- CI validates generated texts but does not publish; document the Cloudflare/Docker deploy path next to `make docker-*`.
- Skills YAML is very long (~200 lines). Consider trimming public skills to a curated set and keeping a fuller list only in PDF/JSON for readability.
- `dateModified` is manual — add a CI check that fails (or warns) if CV YAML changes without bumping it.

**`jaswdr/cv`**

- PDF is linked from the site but lives in a separate Pages deploy. Either:
  - Keep as-is and document the dual-source workflow, or
  - Generate/copy PDF into the Hugo `static/` tree on release to remove an external dependency.

**This repo**

- After redirect ships, add a short `CONTRIBUTING` / ownership note: “Do not edit CV content here; edit `jaswdr/blog`.”

### 4. Product improvements for the live site (in `jaswdr/blog`)

Prioritize in this order once hosting is clear:

1. **Projects section** — Featured open source (`faker` ~644★, `docker-image-sybase`, Neovim plugins). Pull metadata from YAML, not live GitHub API calls at build time unless a refresh job is added.
2. **Selected writing** — If the `blog` name should mean something, add Markdown posts under `content/en/posts/` and re-enable sitemap/RSS only for that section.
3. **Accessibility & polish** — Already has skip link and theme toggle; audit contrast in light/dark, keyboard focus rings, and reduced-motion preference.
4. **Performance** — Portrait and OG generation are build-time; keep fonts system-only (current approach). Avoid adding third-party analytics without a clear need.
5. **Hireable signal** — Profile marks hireable; optionally surface a clear CTA (email / LinkedIn) without turning the page into a marketing site.
6. **i18n** — Data is under `data/en/`; Portuguese secondary locale is a later option if desired, not a near-term need.

### 5. Tooling and quality bar (follow `blog` patterns)

If this repo ever hosts more than a redirect:

- Pin the SSG version (Hugo 0.163.3 today in `blog`).
- CI: build + validate machine-readable outputs (mirror `blog`’s workflow).
- Pre-commit hooks for generated `cv.*` / `llms.txt` consistency.
- Security headers belong at the CDN/nginx edge (already handled in `blog`’s `nginx.conf` / `security-headers.conf`); GitHub Pages cannot replicate those 1:1.

### 6. Explicit non-goals (near term)

- Rebuilding the CV in Jekyll inside this repo.
- Adding a JS framework SPA for a mostly static identity page.
- Mirroring full CV content in multiple repos without a single source of truth.
- Calendar-driven rewrite of the visual design unless content structure changes.

---

## Suggested implementation sequence

```text
1. Hygiene redirect + README in jaswdr.github.io
2. Confirm Option A/B/C for DNS
3. Doc fix & rename clarity in jaswdr/blog
4. Projects YAML section on jaswdr.dev
5. Optional: posts / sitemap / PDF packaging
6. Optional: repo rename blog → website (with redirects)
```

Each step should be a separate PR so hosting changes are reversible.

---

## Success criteria

- Visiting https://jaswdr.github.io/ lands users on https://jaswdr.dev/ (or a clear interim page linking there).
- `jaswdr.dev` remains the only canonical CV URL (`rel=canonical`, JSON-LD `url`, Open Graph).
- CV content has a single edit path (`data/en/` in the Hugo site).
- README in this repo accurately describes role in the ecosystem (no leftover “empty Jekyll user site” impression).
- Optional stretch: `jaswdr.dev` shows featured projects without manual HTML edits.

---

## Context sources used for this plan

- Local tree of `jaswdr/jaswdr.github.io` (this repo)
- Live https://jaswdr.github.io/ and https://jaswdr.dev/
- Source inspection of [`jaswdr/blog`](https://github.com/jaswdr/blog) (Hugo layouts, Makefile, Dockerfile, CI)
- Public GitHub profile and notable repos (especially `jaswdr/faker`)
