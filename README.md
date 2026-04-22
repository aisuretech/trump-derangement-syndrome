# TDS Watch: AI-Powered Satirical Political Commentary Site (Next.js + Supabase)

TDS Watch is a satirical, AI-assisted political commentary website that publishes daily "TDS moments" and long-form explainer content around Trump Derangement Syndrome as a cultural term.

Built for readers, satirical media creators, and web developers who want to study a production Next.js App Router project with SEO metadata, JSON-LD, RSS, scheduled content generation, and Supabase-backed publishing.

## Live Demo
- Production: https://tds.policyclown.com

<!-- Use absolute production URLs for README images (https://...), not relative paths. -->
![TDS Watch homepage and brand preview](https://tds.policyclown.com/tds1.png)

## Why This Project Is Discoverable on GitHub
- Uses highly searchable keywords across title, routes, metadata, and content: TDS Watch, Trump Derangement Syndrome, satirical political commentary, Next.js App Router.
- Documents end-to-end architecture: ingestion pipeline, AI generation, Supabase schema, cron publishing, and SEO surface.
- Includes practical setup commands and environment variables that map directly to real scripts and runtime behavior.
- Exposes crawlable content assets (sitemap, robots, RSS, canonical URLs, Open Graph, JSON-LD) that mirror production SEO workflows.

## Table of Contents
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Environment Variables](#environment-variables)
- [Architecture](#architecture)
- [Learning Modules](#learning-modules)
- [Deployment](#deployment)
- [SEO and Performance](#seo-and-performance)
- [Screenshots](#screenshots)
- [Use Cases](#use-cases)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [FAQ](#faq)
- [Suggested GitHub Topics](#suggested-github-topics)
- [Links](#links)
- [License](#license)

## Features

### For Learners
- Explore a real App Router content site with dynamic post routes (`/year/month/slug`) and metadata generation.
- Learn how daily editorial posts can be generated from external news + LLM prompts + safety checks.
- Study practical SEO implementation: canonical tags, Open Graph, Twitter cards, sitemap, robots, and JSON-LD.
- Review a satirical editorial model that separates source summary, analysis, disclaimers, and legal pages.

### For Developers
- Supabase-backed content model with indexed tables (`posts`, `pipeline_logs`) and row-level security policies.
- Server route for scheduled publishing (`/api/jobs/daily`) with auth, backfill date support, and logging.
- Content ingestion pipeline with provider fallback (GNews primary, NewsAPI fallback), scoring, slug de-duplication, and persistence.
- Built-in RSS feed generation (`/rss.xml`) and tag archive pages for discoverability and internal linking.

## Tech Stack
- Frontend: Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS v4
- Runtime/Platform: Node.js on Vercel with Vercel Cron jobs
- Analytics and Monitoring: Vercel runtime logs + Supabase `pipeline_logs` table
- Testing: ESLint static checks + manual pipeline test UI (`/test`)
- PWA: Not configured yet (no service worker or web app manifest)

## Quick Start

### Prerequisites
- Node.js 20+ and npm
- Supabase project, OpenAI API key, and at least one news provider API key (GNews or NewsAPI)

### Install and Run
```bash
npm install
npm run dev
```

Open http://localhost:3000

### Build and Validate
```bash
npm run lint
npm run build
npm run start
```

## Project Structure
```text
tds/
├─ app/
│  ├─ layout.tsx
│  ├─ page.tsx
│  ├─ [year]/[month]/[slug]/page.tsx
│  ├─ archive/page.tsx
│  ├─ tags/[slug]/page.tsx
│  ├─ trump-derangement-syndrome/page.tsx
│  ├─ calm/page.tsx
│  ├─ about/page.tsx
│  ├─ api/jobs/daily/route.ts
│  ├─ rss.xml/route.ts
│  ├─ sitemap.ts
│  └─ robots.ts
├─ components/
│  ├─ Header.tsx
│  ├─ Footer.tsx
│  ├─ PostCard.tsx
│  ├─ TdsMeter.tsx
│  └─ Testimonials.tsx
├─ lib/
│  ├─ seo.ts
│  ├─ supabase.ts
│  ├─ types.ts
│  └─ content/
│     ├─ sources.ts
│     ├─ select.ts
│     ├─ generate.ts
│     └─ safety.ts
├─ supabase/migrations/001_initial.sql
├─ public/
├─ vercel.json
└─ package.json
```

## Environment Variables
Create a .env file in the project root:

```env
NEXT_PUBLIC_SITE_URL=https://tds.policyclown.com

NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_role_key

OPENAI_API_KEY=your_openai_api_key
OPENAI_MODEL=gpt-4o-mini

GNEWS_API_KEY=your_gnews_api_key
NEWSAPI_API_KEY=your_newsapi_api_key

CRON_SECRET=your_secure_cron_token
NEXT_PUBLIC_CRON_SECRET=your_local_test_token

DISABLE_PUBLISHING=false
SITE_CONTENT_UPDATED_AT=2026-04-21
```

## Architecture
The application is a content-first Next.js platform where public routes read from Supabase, while a protected cron endpoint ingests source articles, generates satirical structured content with an LLM, enforces safety rules, and publishes one dated post entry.

1. Scheduled trigger calls `/api/jobs/daily` on Vercel Cron with `CRON_SECRET` bearer auth.
2. The route fetches source candidates from GNews, falling back to NewsAPI if needed, then removes already-used source hashes.
3. Best candidate is selected and sent to OpenAI prompt generation for title, summary, analysis, tags, and SEO description.
4. Output is safety-filtered, slug-normalized, inserted into Supabase `posts`, and the run result is logged to `pipeline_logs`.
5. Public pages, RSS, sitemap, and metadata consume published posts for daily site updates and search/social discoverability.

```text
[Vercel Cron] --> [/api/jobs/daily]
                    |
                    v
        [Fetch Sources: GNews -> NewsAPI fallback]
                    |
                    v
          [Score + Select Candidate Article]
                    |
                    v
         [OpenAI Generation + Safety Filters]
                    |
                    v
        [Supabase posts + pipeline_logs write]
                    |
       +------------+------------+
       |                         |
       v                         v
[Next.js Routes]            [RSS + Sitemap]
(Home/Archive/Post/Tags)    (/rss.xml, /sitemap.xml)
```

## Learning Modules
- Build AI-assisted editorial pipelines with input sourcing, ranking, and controlled generation.
- Implement modern SEO in Next.js App Router using metadata APIs and JSON-LD script blocks.
- Model publish-ready content in Supabase with indexing, unique constraints, and RLS policies.
- Design satirical content experiences with route-level pages like Archive, Calm, and long-form field guides.

## Deployment
Recommended deployment target: Vercel

```bash
npm run build
npm run start
```

Production URL:
- https://tds.policyclown.com

## SEO and Performance
- Canonical URLs are generated per page and include query-aware canonical handling on archive pages.
- Structured data includes `Organization`, `WebSite`, `Article`, and `BreadcrumbList` JSON-LD entities.
- Dynamic sitemap includes static pages, tag archives, and post URLs with priority and change frequency metadata.
- Robots policy allows public crawl while excluding framework internals and API endpoints.
- Open Graph and Twitter metadata are set at layout and route level for social previews.
- RSS feed is generated from the latest posts to improve syndication and subscriber discovery.

## Screenshots
<!-- Use absolute production URLs for all screenshot images (https://...), not ./assets paths. -->

| Feature | Preview |
|--------|--------|
| Archive Index and Tag Browsing | ![](https://tds.policyclown.com/archive-hero.jpg) |
| Calm Perspective Resource Page | ![](https://tds.policyclown.com/calm-hero.jpg) |

## Use Cases
- Launch a satirical or editorial daily content site with automated publishing workflows.
- Use as a reference architecture for Next.js + Supabase + scheduled server routes.
- Prototype structured AI writing pipelines with safety guardrails and source grounding.
- Study practical SEO implementation for small media properties on Vercel.

## Roadmap
- [ ] Add automated unit/integration tests for pipeline selection and metadata generation.
- [ ] Implement per-post dynamic OG image generation for richer social previews.
- [ ] Add first-party analytics dashboard for content performance by tag and source type.
- [ ] Expand internal editorial review mode before publish for optional human approval.

## Contributing
Contributions are welcome.

1. Fork the repository
2. Create a feature branch: git checkout -b feature/your-feature
3. Commit your changes
4. Push to your branch
5. Open a pull request

## FAQ

### What does this project actually publish each day?
It publishes one satirical daily entry sourced from current political coverage, with a factual summary, a satirical analysis section, tags, and SEO metadata.

### Is this a full backend CMS?
No. This project uses Supabase tables as a lightweight content store and publishing log. There is no separate admin CMS UI in the current implementation.

### Can I run this without Vercel Cron?
Yes. You can trigger the daily route manually (including via the internal `/test` page), as long as required environment variables are configured.

### Is this content real news reporting?
No. The site is explicitly satirical/editorial and includes disclaimers that content is AI-generated commentary for entertainment.

## Suggested GitHub Topics
Add these in repository settings for discoverability:
- nextjs
- app-router
- supabase
- openai
- seo
- rss
- satirical-news
- vercel

## Links
- GitHub Organization: https://github.com/aisuretech/
- Website: https://tds.policyclown.com
- Contact: info@AISureTech.com

## License
UNLICENSED (All rights reserved)
