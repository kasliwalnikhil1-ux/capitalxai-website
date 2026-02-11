# EEAT Principles

Google's EEAT framework (Experience, Expertise, Authoritativeness, Trustworthiness) guides how content quality is evaluated. This applies to both SEO rankings and AI answer selection.

## The Four Pillars

### Experience
First-hand or life experience with the topic.

**Signals:**
- Personal anecdotes and case studies
- "I tested this" content
- Real-world results and screenshots
- User-generated reviews

**Implementation:**
- Include author bios with relevant experience
- Add "About the Author" sections
- Feature customer testimonials
- Show real examples, not just theory

### Expertise
Knowledge and skill in the subject area.

**Signals:**
- Credentials and qualifications
- Depth of content coverage
- Technical accuracy
- Citations to authoritative sources

**Implementation:**
- Display author credentials
- Link to primary sources
- Cover topics comprehensively
- Keep content technically accurate and updated

### Authoritativeness
Recognition as a go-to source in the field.

**Signals:**
- Backlinks from respected sites
- Mentions in industry publications
- Social proof and follower counts
- Brand recognition

**Implementation:**
- Build thought leadership content
- Contribute to industry publications
- Maintain consistent publishing
- Develop recognizable brand voice

### Trustworthiness
Accuracy, transparency, and legitimacy.

**Signals:**
- Clear authorship and contact info
- Accurate, fact-checked content
- Secure website (HTTPS)
- Privacy policy and terms

**Implementation:**
- Display clear author attribution
- Include publication and update dates
- Provide contact information
- Use HTTPS and maintain security

## Astro Implementation

### Content Collection Schema with EEAT Fields

Define frontmatter schemas in `src/content/config.ts` to enforce EEAT metadata on every blog post:

```typescript
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

const blog = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string(),
    author: z.string(),
    authorRole: z.string().optional(),         // "CEO, CapitalxAI"
    authorCredentials: z.array(z.string()).optional(), // ["CFA", "10+ years in fintech"]
    publishDate: z.coerce.date(),
    updatedDate: z.coerce.date().optional(),
    tags: z.array(z.string()).default([]),
    image: z.string().optional(),
    imageAlt: z.string().optional(),
    sources: z.array(z.string()).optional(),    // Citation URLs
    reviewedBy: z.string().optional(),          // Expert reviewer name
    draft: z.boolean().default(false),
  }),
});

export const collections = { blog };
```

### Blog Layout with EEAT Signals

Display author credentials, dates, and structured data in `BlogLayout.astro`:

```astro
---
// In BlogLayout.astro frontmatter
const {
  title, description, author, authorRole,
  publishDate, updatedDate, tags, image
} = Astro.props;

const structuredData = {
  '@context': 'https://schema.org',
  '@type': 'BlogPosting',
  headline: title,
  description: description,
  author: {
    '@type': 'Person',
    name: author,
    ...(authorRole && { jobTitle: authorRole }),
  },
  publisher: {
    '@type': 'Organization',
    name: 'CapitalxAI',
    url: siteUrl,
    logo: {
      '@type': 'ImageObject',
      url: new URL('/capitalxai-logo.png', siteUrl).href,
    },
  },
  datePublished: publishDate.toISOString(),
  ...(updatedDate && { dateModified: updatedDate.toISOString() }),
  keywords: tags.join(', '),
};
---

<!-- Render structured data -->
<script type="application/ld+json" set:html={JSON.stringify(structuredData)} />

<!-- Author section with EEAT signals -->
<div class="article-meta">
  <span class="article-author" itemprop="author">{author}</span>
  {authorRole && <span class="author-role">{authorRole}</span>}
  <time datetime={publishDate.toISOString()} itemprop="datePublished">
    {formattedDate}
  </time>
  {updatedDate && (
    <span class="updated-label">
      Updated <time datetime={updatedDate.toISOString()} itemprop="dateModified">
        {formattedUpdatedDate}
      </time>
    </span>
  )}
</div>
```

### Blog Post Frontmatter Example

```markdown
---
title: "How to Raise Seed Funding with AI"
description: "A practical guide to leveraging AI-powered tools for startup fundraising."
author: "CapitalxAI Team"
authorRole: "AI & Fundraising Experts"
publishDate: 2025-06-15
updatedDate: 2025-08-01
tags: ["fundraising", "AI", "seed funding"]
sources:
  - "https://www.crunchbase.com/research"
  - "https://pitchbook.com/data"
---
```

## YMYL Considerations

"Your Money or Your Life" topics (health, finance, legal, safety) require extra EEAT rigor:

- Medical content reviewed by healthcare professionals
- Financial advice from certified experts
- Legal content reviewed by attorneys
- Clear disclaimers where appropriate
