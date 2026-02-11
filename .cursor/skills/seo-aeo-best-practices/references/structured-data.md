# Structured Data (JSON-LD)

Structured data helps search engines and AI understand your content. JSON-LD is the recommended format.

## Why Structured Data Matters

- **Rich snippets:** Enhanced search result appearance
- **Knowledge panels:** Featured information boxes
- **AI training:** Better content understanding
- **Voice search:** Answer selection for voice queries

## Common Schema Types

### Article / Blog Post

```typescript
const articleSchema = {
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  headline: post.data.title,
  description: post.data.description,
  image: imageUrl,
  datePublished: post.data.publishDate.toISOString(),
  ...(post.data.updatedDate && {
    dateModified: post.data.updatedDate.toISOString(),
  }),
  author: {
    "@type": "Person",
    name: post.data.author,
  },
  publisher: {
    "@type": "Organization",
    name: "CapitalxAI",
    logo: {
      "@type": "ImageObject",
      url: new URL("/capitalxai-logo.png", siteUrl).href,
    },
  },
  mainEntityOfPage: {
    "@type": "WebPage",
    "@id": canonicalUrl,
  },
  keywords: post.data.tags.join(", "),
};
```

### FAQ Page

```typescript
const faqSchema = {
  "@context": "https://schema.org",
  "@type": "FAQPage",
  mainEntity: faqs.map((faq) => ({
    "@type": "Question",
    name: faq.question,
    acceptedAnswer: {
      "@type": "Answer",
      text: faq.answer,
    },
  })),
};
```

### Organization

```typescript
const orgSchema = {
  "@context": "https://schema.org",
  "@type": "Organization",
  name: "CapitalxAI",
  url: "https://capitalxai.com",
  logo: "https://capitalxai.com/capitalxai-logo.png",
  sameAs: [
    "https://twitter.com/capitalxai",
    "https://linkedin.com/company/capitalxai",
  ],
  contactPoint: {
    "@type": "ContactPoint",
    contactType: "customer service",
  },
};
```

### SoftwareApplication (SaaS Product)

```typescript
const appSchema = {
  "@context": "https://schema.org",
  "@type": "SoftwareApplication",
  name: "CapitalxAI",
  applicationCategory: "BusinessApplication",
  operatingSystem: "Web",
  description: description,
  url: siteUrl,
  offers: {
    "@type": "Offer",
    price: "0",
    priceCurrency: "USD",
  },
  featureList: [
    "AI powered investor discovery",
    "Fast and accurate web research",
    "Agent driven workflows",
  ],
};
```

### BreadcrumbList

```typescript
const breadcrumbData = {
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  itemListElement: [
    { "@type": "ListItem", position: 1, name: "Home", item: siteUrl },
    { "@type": "ListItem", position: 2, name: "Blog", item: `${siteUrl}/blog` },
    { "@type": "ListItem", position: 3, name: title, item: canonicalUrl },
  ],
};
```

## Implementation in Astro

### Using `set:html` in Layout Frontmatter

Astro uses `set:html` to safely inject JSON-LD into `<script>` tags. Build the structured data object in the frontmatter, then render it in the `<head>`:

```astro
---
// Build structured data in frontmatter
const structuredData = {
  '@context': 'https://schema.org',
  '@type': 'BlogPosting',
  headline: title,
  description: description,
  datePublished: publishDate.toISOString(),
  author: { '@type': 'Person', name: author },
  publisher: {
    '@type': 'Organization',
    name: 'CapitalxAI',
    url: siteUrl,
  },
};
---

<head>
  <!-- Other meta tags... -->
  <script type="application/ld+json" set:html={JSON.stringify(structuredData)} />
</head>
```

### Multiple Schemas on One Page

You can include multiple JSON-LD blocks (e.g., article + breadcrumbs) as separate `<script>` tags:

```astro
<Fragment slot="head">
  <script type="application/ld+json" set:html={JSON.stringify(articleSchema)} />
  <script type="application/ld+json" set:html={JSON.stringify(breadcrumbData)} />
</Fragment>
```

### Injecting via Named Slots

Use Astro's slot system to let child layouts inject structured data into the parent `<head>`:

```astro
<!-- BaseLayout.astro -->
<head>
  <!-- Base meta tags -->
  <slot name="head" />
</head>

<!-- BlogLayout.astro -->
<BaseLayout title={title} description={description}>
  <Fragment slot="head">
    <script type="application/ld+json" set:html={JSON.stringify(structuredData)} />
  </Fragment>
  <slot />
</BaseLayout>
```

## Testing Tools

- [Google Rich Results Test](https://search.google.com/test/rich-results)
- [Schema.org Validator](https://validator.schema.org/)
