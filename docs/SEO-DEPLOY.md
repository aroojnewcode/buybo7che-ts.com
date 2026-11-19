# SEO & canonical host

## Canonical domain

Production canonical origin is **`https://buybo7cheats.com`** (`src/config/site.ts`, `scripts/cf-build.mjs`, sitemap finalize).

1. Add **buybo7cheats.com** (and `www` if used) as a **Custom Domain** on the Cloudflare Worker `buybo7cheats-com` in the same account as the zone. Enable **Always Use HTTPS** and a **www → apex** redirect in the zone (Workers `_redirects` must use relative paths only).
2. When the zone is available, uncomment and set `[[routes]]` in `wrangler.toml` (see file comments).
3. In **Google Search Console**, use one **Domain** or **URL-prefix** property for `https://buybo7cheats.com/` only.
4. Submit `https://buybo7cheats.com/sitemap-index.xml` (also linked from `/robots.txt` on the live host).

Builds set `SITE_ORIGIN` and `SITEMAP_ORIGIN` to buybo7cheats.com so sitemap `<loc>` values match HTML `<link rel="canonical">` on the custom domain.

`npm run build` runs `scripts/emit-redirects.mjs`, which generates **`public/_redirects`** from `PATH_REDIRECTS` in `scripts/redirects.mjs` (301 + slash variants). Legacy Astro HTML redirect stubs are not used in production—edge 301s only.

`/go/*` checkout gateway pages stay indexable but are **omitted from the sitemap** (`src/config/sitemap.ts`).

## IndexNow

Set these in **Cloudflare Workers Builds → Variables** (or local `.env` via `npm run env:init`):

| Variable | Purpose |
|----------|---------|
| `SITE_ORIGIN` | `https://buybo7cheats.com` |
| `SITEMAP_ORIGIN` | `https://buybo7cheats.com` |
| `INDEXNOW_KEY` | 32-char hex; served at `https://buybo7cheats.com/<key>.txt` |
| `INDEXNOW_HOST` | `buybo7cheats.com` |
| `PUBLIC_GA_MEASUREMENT_ID` | GA4 `G-XXXXXXXXXX` |

`INDEXNOW_KEY` triggers `scripts/indexnow.mjs` after build. `/go/*` are full HTML gateways (`src/pages/go/[slug].astro`) with self-canonical on buybo7cheats.com — do not re-add Astro middleware or `serve.mjs` 302s to Zadeyo. HSTS is set in `public/_headers` (and echoed by the minimal `src/worker.js` wrapper).

## Meta & structured data

- Per-route titles/descriptions: `src/config/pageSeo.ts`, `pageSeoBlog.ts`, `forumSeo.ts`
- Organization `sameAs`: `PUBLIC_ORG_SAME_AS` or default Discord invite from `src/config/site.ts`
