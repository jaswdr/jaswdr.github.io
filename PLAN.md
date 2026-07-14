# Plan: Improve `jaswdr.github.io`

## Current direction

This repository **is** the GitHub user profile site (https://jaswdr.github.io/). Keep it as a public profile landing page; do not replace it with a blank redirect.

The long-form CV remains at https://jaswdr.dev/ ([`jaswdr/blog`](https://github.com/jaswdr/blog)). This profile should introduce who Jonathan is, highlight the path and open source, and send people to the full CV, GitHub, and email.

## Done in the latest revision

- Static profile page (`index.html`) with brand-first hero and portrait plane
- Distinct visual language from jaswdr.dev (teal/mist + Bricolage Grotesque / Figtree)
- Sections: About, Selected path, Focus, Open source, Connect
- Motion: hero entrance + scroll reveals (respects `prefers-reduced-motion`)
- Portrait assets and favicons under `assets/images/`
- `.nojekyll` for plain static GitHub Pages serving

## Next improvements

1. **Content**
   - Optionally feature 1–2 more open-source projects beside `faker`
   - Keep timeline short; full history stays on jaswdr.dev
2. **Ops**
   - Confirm GitHub Pages is serving from `master` / root
   - Optional: pin a custom domain later only if consolidating with jaswdr.dev
3. **Polish**
   - Generate a dedicated Open Graph image (1200×630) instead of the square portrait
   - Compress / responsive-src portrait further if Lighthouse flags LCP
4. **Cross-repo**
   - Fix `jaswdr/blog` README clone URL (`jaswdr/website` → `jaswdr/blog`)
   - Keep PDF/CV updates in their source repos; link out from here

## Non-goals

- Duplicating the full YAML CV from `jaswdr/blog` into this repo
- Rebuilding jaswdr.dev inside this repository
- Introducing a JS framework for a static profile page
