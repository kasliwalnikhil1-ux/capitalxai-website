# Technical SEO Checklist

Essential technical SEO elements for Astro-based web applications.

## Metadata

### Title Tags
- Unique per page
- 50-60 characters
- Primary keyword near the beginning
- Brand name at the end (optional)

### Meta Descriptions
- Unique per page
- 150-160 characters
- Include call-to-action
- Contain relevant keywords

### Open Graph
```html
<meta property="og:title" content="Page Title" />
<meta property="og:description" content="Description" />
<meta property="og:image" content="https://example.com/image.jpg" />
<meta property="og:url" content="https://example.com/page" />
<meta property="og:type" content="article" />
```

### Astro Layout Implementation

Pass metadata via layout props and render in `<head>`:

```astro
---
// BaseLayout.astro
interface Props {
  title: string;
  description: string;
  canonical?: string;
  ogImage?: string;
  ogType?: 'website' | 'article';
  noindex?: boolean;
}

const { title, description, canonical, ogImage, ogType = 'website', noindex = false } = Astro.props;
const siteUrl = Astro.site?.toString() ?? 'https://capitalxai.com';
const canonicalUrl = canonical
  ? new URL(canonical, siteUrl).href
  : new URL(Astro.url.pathname, siteUrl).href;
const imageUrl = ogImage
  ? new URL(ogImage, siteUrl).href
  : new URL('/Open Graph CapitalxAI.png', siteUrl).href;
---

<head>
  <title>{title}</title>
  <meta name="description" content={description} />
  {noindex ? <meta name="robots" content="noindex, nofollow" /> : null}
  <link rel="canonical" href={canonicalUrl} />

  <!-- Open Graph -->
  <meta property="og:type" content={ogType} />
  <meta property="og:title" content={title} />
  <meta property="og:description" content={description} />
  <meta property="og:url" content={canonicalUrl} />
  <meta property="og:image" content={imageUrl} />
  <meta property="og:site_name" content="CapitalxAI" />

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content={title} />
  <meta name="twitter:description" content={description} />
  <meta name="twitter:image" content={imageUrl} />

  <!-- Child layouts can inject extra head content -->
  <slot name="head" />
</head>
```

### Article-Specific Meta (Blog Layout)

Use a `<Fragment slot="head">` to inject article-specific OG tags from `BlogLayout.astro`:

```astro
<BaseLayout title={`${title} | CapitalxAI Blog`} description={description} ogType="article">
  <Fragment slot="head">
    <meta property="article:published_time" content={publishDate.toISOString()} />
    {updatedDate && <meta property="article:modified_time" content={updatedDate.toISOString()} />}
    {tags.map((tag) => <meta property="article:tag" content={tag} />)}
  </Fragment>
  <!-- page content -->
</BaseLayout>
```

## Sitemaps

### Using @astrojs/sitemap

The `@astrojs/sitemap` integration auto-generates a sitemap from all static pages at build time. Configure it in `astro.config.mjs`:

```javascript
// astro.config.mjs
import { defineConfig } from 'astro/config';
import sitemap from '@astrojs/sitemap';

export default defineConfig({
  site: 'https://capitalxai.com',
  integrations: [
    sitemap({
      // Optional: filter or customize entries
      filter: (page) => !page.includes('/404'),
      changefreq: 'weekly',
      priority: 0.8,
    }),
  ],
});
```

The sitemap is automatically built to `dist/sitemap-index.xml` and `dist/sitemap-0.xml` during `astro build`.

## Canonical URLs

Astro provides `Astro.url` and `Astro.site` for building canonical URLs in frontmatter:

```astro
---
const siteUrl = Astro.site?.toString() ?? 'https://capitalxai.com';
const canonicalUrl = new URL(Astro.url.pathname, siteUrl).href;
---
<link rel="canonical" href={canonicalUrl} />
```

## Redirects

### Astro Config Redirects

Define redirects in `astro.config.mjs`:

```javascript
// astro.config.mjs
export default defineConfig({
  redirects: {
    '/old-page': '/new-page',
    '/blog/old-slug': '/blog/new-slug',
    // Dynamic patterns
    '/posts/[...slug]': '/blog/[...slug]',
  },
});
```

### Meta Refresh (for simple cases)

```astro
---
// src/pages/old-page.astro
---
<meta http-equiv="refresh" content="0;url=/new-page" />
```

## Performance

[Core Web Vitals](https://web.dev/articles/defining-core-web-vitals-thresholds) impact rankings:

- **LCP (Largest Contentful Paint):** < 2.5s
- **INP (Interaction to Next Paint):** < 200ms
- **CLS (Cumulative Layout Shift):** < 0.1

### HTML Compression

Enable in `astro.config.mjs`:
```javascript
export default defineConfig({
  compressHTML: true,
});
```

### Image Optimization

Use Astro's built-in `<Image>` component from `astro:assets`:

```astro
---
import { Image } from 'astro:assets';
import heroImage from '../assets/hero.png';
---
<Image
  src={heroImage}
  alt="Descriptive alt text for SEO"
  width={1200}
  height={630}
  format="webp"
  loading="lazy"
/>
```

Key image practices:
- Use `astro:assets` `<Image>` for automatic optimization (WebP/AVIF)
- Always include descriptive `alt` text
- Set explicit `width` and `height` to prevent CLS
- Use `loading="lazy"` for below-the-fold images
- Use `loading="eager"` for above-the-fold / LCP images

### Inline Stylesheets

Reduce render-blocking CSS by inlining small stylesheets:
```javascript
export default defineConfig({
  build: {
    inlineStylesheets: 'auto', // 'auto' | 'always' | 'never'
  },
});
```

### Font Loading

Prevent layout shift with `font-display: swap` and preconnect hints:

```html
<!-- Preconnect to Google Fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />

<!-- Load with display=swap for no FOIT -->
<link
  href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&display=swap"
  rel="stylesheet"
/>
```

### CSS Minification

Enable via Vite config:
```javascript
export default defineConfig({
  vite: {
    build: {
      cssMinify: true,
    },
  },
});
```

## Robots.txt

Place a static `robots.txt` in the `public/` directory:

```
# public/robots.txt
User-agent: *
Allow: /

Sitemap: https://capitalxai.com/sitemap-index.xml
```

Note: `@astrojs/sitemap` generates `sitemap-index.xml` (not `sitemap.xml`) by default. Make sure `robots.txt` points to the correct path.
