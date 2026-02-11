# AI/AEO Considerations

Answer Engine Optimization (AEO) prepares content to be selected as authoritative answers by AI systems like ChatGPT, Perplexity, Google AI Overviews, and Bing Copilot.

## How AI Selects Answers

AI systems evaluate content based on:

1. **Clarity:** Is the answer direct and easy to extract?
2. **Authority:** Is the source trustworthy?
3. **Comprehensiveness:** Does it fully address the question?
4. **Recency:** Is the information up to date?
5. **Structure:** Can the AI parse and understand it?

## Content Structure for AI

### Direct Answers First
Lead with the answer, then explain.

**Bad:**
> The history of fundraising dates back decades... [500 words later] ...AI can now automate investor discovery.

**Good:**
> AI-powered investor discovery uses machine learning to match startups with the right investors. It works by analyzing...

### Clear Headings
Use descriptive H2/H3 headings that match user questions.

**Bad:** "Overview" → "Details" → "More Information"
**Good:** "What is AI investor discovery?" → "How does it work?" → "When should you use it?"

### Lists and Tables
AI extracts structured information more easily than prose.

```markdown
## Benefits of AI-Powered Fundraising

- **Speed:** Find investors in minutes, not weeks
- **Accuracy:** Match based on real investment criteria
- **Scale:** Research hundreds of investors simultaneously
```

### FAQ Format
Question-answer pairs are ideal for AI extraction.

## Astro Implementation for AEO

### FAQ Content Collection

Define a dedicated FAQ collection in `src/content/config.ts`:

```typescript
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

const faq = defineCollection({
  type: 'data',  // JSON/YAML data, not markdown
  schema: z.object({
    question: z.string(),
    answer: z.string(),
    category: z.string().optional(),
  }),
});

const blog = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string(),
    author: z.string(),
    publishDate: z.coerce.date(),
    updatedDate: z.coerce.date().optional(),
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(false),
  }),
});

export const collections = { blog, faq };
```

### FAQ Page with JSON-LD

Render FAQs with structured data so AI systems can parse them:

```astro
---
// src/pages/faq.astro
import { getCollection } from 'astro:content';
import BaseLayout from '../layouts/BaseLayout.astro';

const faqs = await getCollection('faq');

const faqSchema = {
  '@context': 'https://schema.org',
  '@type': 'FAQPage',
  mainEntity: faqs.map((faq) => ({
    '@type': 'Question',
    name: faq.data.question,
    acceptedAnswer: {
      '@type': 'Answer',
      text: faq.data.answer,
    },
  })),
};
---

<BaseLayout title="FAQ | CapitalxAI" description="Frequently asked questions about CapitalxAI.">
  <Fragment slot="head">
    <script type="application/ld+json" set:html={JSON.stringify(faqSchema)} />
  </Fragment>

  <main>
    <h1>Frequently Asked Questions</h1>
    {faqs.map((faq) => (
      <details>
        <summary>{faq.data.question}</summary>
        <p>{faq.data.answer}</p>
      </details>
    ))}
  </main>
</BaseLayout>
```

## Technical Implementation

### Structured Data (Critical)
JSON-LD helps AI understand content type and relationships. In Astro, use `set:html` to inject it:

```astro
<script type="application/ld+json" set:html={JSON.stringify(faqSchema)} />
```

### Canonical Content
Ensure AI finds your authoritative version, not copies.

- Set canonical URLs using `Astro.url` and `Astro.site`
- Avoid duplicate content across pages
- Use `<link rel="canonical">` in your base layout

```astro
---
const canonicalUrl = new URL(Astro.url.pathname, Astro.site).href;
---
<link rel="canonical" href={canonicalUrl} />
```

### Freshness Signals
AI systems prefer current information.

- Display publish and update dates prominently
- Use `updatedDate` in content collection frontmatter
- Update content regularly (even small updates signal freshness)
- Include `dateModified` in JSON-LD structured data

```markdown
---
# Blog post frontmatter
publishDate: 2025-06-15
updatedDate: 2025-08-01
---
```

## Content Quality Signals

### Author Credentials
AI systems increasingly check author authority.

- Display author name and credentials
- Link to author profiles
- Include author structured data in JSON-LD

### Citations and Sources
Linking to authoritative sources increases trust.

- Cite primary sources
- Link to studies, documentation, official sources
- Avoid circular citations (sites citing each other)

### Comprehensive Coverage
AI prefers content that fully answers questions.

- Cover related questions users might have
- Include definitions for technical terms
- Address common misconceptions

## Measuring AEO Success

### Monitor AI Mentions
Track when AI assistants cite your content:
- Search for your brand + "according to"
- Monitor traffic from AI platforms
- Check Perplexity, Bing Copilot responses

### Track Zero-Click Queries
If AI answers questions directly, traditional rankings matter less.

### Featured Snippet Capture
Featured snippets often become AI answers. Track which you own.

## AEO vs SEO Balance

AEO and SEO largely align—quality content serves both. Key differences:

| Aspect | SEO Focus | AEO Focus |
|--------|-----------|-----------|
| Goal | Rank on page 1 | Be THE answer |
| Format | Varies | Direct, structured |
| Length | Often longer | Concise + comprehensive |
| Links | Link building | Source citations |
