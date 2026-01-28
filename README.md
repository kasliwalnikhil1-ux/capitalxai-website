# CapitalxAI Marketing Website

Modern, high-performance marketing site for CapitalxAI built with [Astro](https://astro.build). Dark theme, SEO-optimized, mobile-first, and focused on Core Web Vitals.

## Features

- **Framework:** Astro (static by default, minimal JS)
- **Theme:** Dark, professional fintech/AI aesthetic
- **SEO:** Meta tags, Open Graph, Twitter Card, canonical URLs, JSON-LD structured data (SaaS), static `sitemap.xml`, `robots.txt`
- **Performance:** Static rendering, lazy-loaded images, optimized fonts, compressed HTML
- **Accessibility:** Semantic HTML, ARIA where needed, clear heading hierarchy

## Project Structure

```
capitalxai-website/
├── public/           # Static assets (favicon, robots.txt)
├── src/
│   ├── components/   # Reusable UI (Header, Hero, TrustSection, Features, Footer, OptimizedImage)
│   ├── layouts/      # BaseLayout.astro (SEO, meta, global styles)
│   ├── pages/        # index, privacy, terms, contact
│   └── styles/       # global.css
├── astro.config.mjs
├── package.json
└── tsconfig.json
```

## Commands

| Command        | Action           |
|----------------|------------------|
| `npm install`  | Install deps     |
| `npm run dev`  | Start dev server |
| `npm run build`| Production build |
| `npm run preview` | Preview build  |

## SEO Checklist

- [x] Title and meta description per page
- [x] Canonical URL
- [x] Open Graph and Twitter Card tags
- [x] JSON-LD structured data (SoftwareApplication)
- [x] Semantic headings (h1 → h2 → h3)
- [x] Sitemap (`public/sitemap.xml`; add pages when you add routes)
- [x] `robots.txt` in `public/`
- [x] Image optimization via `astro:assets` and `OptimizedImage.astro`

## Customization

- **Site URL:** Set `site` in `astro.config.mjs` (used for canonical, sitemap, OG).
- **Trust logos:** Replace placeholder text in `TrustSection.astro` with real logo images (use `OptimizedImage` and keep grayscale/minimal style).
- **OG image:** Add `public/og-image.png` (recommended 1200×630) for social previews.
- **Fonts:** Loaded from Google Fonts (DM Sans, JetBrains Mono); can switch to self-hosted for privacy/performance.

## License

Private – CapitalxAI.
