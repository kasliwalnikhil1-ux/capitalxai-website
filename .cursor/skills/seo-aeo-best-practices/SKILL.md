---
name: seo-aeo-best-practices
description: SEO and AEO (Answer Engine Optimization) best practices for Astro sites, including EEAT principles, structured data (JSON-LD), and technical SEO. Use when implementing metadata, sitemaps, structured data, or optimizing content for search engines and AI assistants.
license: MIT
metadata:
  author: capitalxai
  version: "1.0.0"
  framework: astro
---

# SEO & AEO Best Practices for Astro

Principles for optimizing Astro-based websites for both traditional search engines (SEO) and AI-powered answer engines (AEO). Includes Google's EEAT guidelines and structured data implementation using Astro's component model, content collections, and `set:html` directive.

## When to Apply

Reference these guidelines when:
- Implementing `<head>` metadata and Open Graph tags in Astro layouts
- Configuring `@astrojs/sitemap` and `robots.txt`
- Adding JSON-LD structured data via `set:html`
- Writing or updating Astro content collections (blog posts, pages)
- Optimizing content for featured snippets and AI assistants (ChatGPT, Perplexity, etc.)
- Evaluating content quality using EEAT principles

## Core Concepts

### SEO (Search Engine Optimization)
Optimizing content to rank well in traditional search results (Google, Bing).

### AEO (Answer Engine Optimization)
Optimizing content to be selected as authoritative answers by AI systems.

### EEAT (Experience, Expertise, Authoritativeness, Trustworthiness)
Google's framework for evaluating content quality.

## Astro-Specific Patterns

### Structured Data
Astro uses `set:html` to inject JSON-LD safely in `<script>` tags:
```astro
<script type="application/ld+json" set:html={JSON.stringify(structuredData)} />
```

### Metadata
Astro layouts pass metadata via props and render them in the frontmatter-to-`<head>` pipeline:
```astro
---
const { title, description } = Astro.props;
---
<head>
  <title>{title}</title>
  <meta name="description" content={description} />
</head>
```

### Content Collections
Blog posts use Zod-validated schemas in `src/content/config.ts` with frontmatter fields for SEO (title, description, publishDate, tags, etc.).

### Sitemap
Generated automatically via `@astrojs/sitemap` integration in `astro.config.mjs` using the configured `site` URL.

## References

See `references/` for detailed guidance:
- `eeat-principles.md` — EEAT implementation (Astro content collections & layouts)
- `structured-data.md` — Structured data patterns (JSON-LD with `set:html`)
- `technical-seo.md` — Technical SEO checklist (Astro metadata, sitemap, images, performance)
- `aeo-considerations.md` — AI/AEO considerations (content structure & schema patterns)
