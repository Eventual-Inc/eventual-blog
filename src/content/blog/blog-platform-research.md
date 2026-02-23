---
title: "Blog platform research for eventual.ai"
description: "Research on static site generators, headless CMS options, and the recommended stack for the Eventual blog."
pubDate: "Feb 22 2026"
heroImage: "../../assets/blog-placeholder-3.jpg"
---


> Research conducted 2026-02-22. Covers static site generators, headless CMS options, and the recommended stack for a blog at `blog.eventual.ai` or `eventual.ai/blog`.

## Requirements

- Custom domain/subdomain (`blog.eventual.ai` or `eventual.ai/blog`)
- Markdown-based writing with Git workflow
- Optional GUI/CMS for non-technical contributors
- Good SEO
- Support for embedded media (YouTube, images, videos)
- Newsletter/email capability (optional)
- Access management for guest authors

---

## Recommendation: Astro + TinaCMS

**Astro** for the static site generator, **TinaCMS** for the optional visual editing layer. Deploy on Vercel, Netlify, or Cloudflare Pages (free). Point `blog.eventual.ai` via DNS CNAME.

### Why this combo

- Content lives as `.md` files in Git — you write in your editor, commit, push, done
- Non-technical people get a visual editor (TinaCMS) that looks like WordPress but commits to the same Git repo
- Astro ships zero JavaScript by default — near-perfect Lighthouse SEO scores
- 2-3x faster than Next.js for content sites in real benchmarks

### Developer workflow

```
1. Write posts/my-post.md in your editor
2. git commit && git push
3. CI builds and deploys automatically
4. Done
```

### Non-technical editor workflow

```
1. Open the TinaCMS visual editor in browser
2. Click on text, edit inline with real-time preview
3. Hit save — TinaCMS creates a Git commit automatically
4. CI builds and deploys automatically
```

---

## Comparison Table

| | Markdown in Git | SEO | Simplicity | Non-tech editors | Newsletters | Access mgmt |
|---|---|---|---|---|---|---|
| **Astro only** | Native | Best (zero JS) | Simplest | None (dev only) | DIY integrate | N/A |
| **Astro + TinaCMS** | Native | Best (zero JS) | Simple | Good (visual editor) | DIY integrate | Role-based |
| **Next.js + TinaCMS** | Native | Great | More complex | Good | DIY integrate | Role-based |
| **Ghost** | Database (not Git) | Great | Medium (needs server) | Excellent | Built-in | Built-in |
| **WordPress** | Plugin hack | Good | Heavy | Excellent | Plugin-based | Built-in |
| **Strapi** | Database (not Git) | N/A (headless) | Complex (full backend) | Moderate | Separate | RBAC (3 roles free) |

---

## Astro vs Next.js for Blogs

Astro wins for blogs specifically:

- **SEO:** Pure static HTML by default. Near-perfect Lighthouse scores. Faster FCP, LCP, TTI.
- **Performance:** 90% less JavaScript than Next.js (which still bundles React runtime for static pages). 2-3x faster in real benchmarks.
- **Cost:** Static files = free hosting. Next.js SSR needs server compute that scales with traffic.
- **Simplicity:** Astro's mental model is simpler for content sites.
- **Next.js is better for:** SaaS apps, dashboards, e-commerce — things with heavy interactivity. Overkill for a blog.

Sources: [Senorit benchmarks](https://senorit.de/en/blog/astro-vs-nextjs-2025), [Pagepro comparison](https://pagepro.co/blog/astro-nextjs/), [BetterLink 40% faster analysis](https://eastondev.com/blog/en/posts/dev/20251202-astro-vs-nextjs-comparison/), [Sourabh Yadav blog comparison](https://sourabhyadav.com/blog/astro-vs-nextjs-for-blogs-2026/)

---

## TinaCMS Details

### Editor experience
- Smashing Magazine uses TinaCMS. Their team said: "looks like WordPress, smells like WordPress, but produces hard files that get committed directly to our repo."
- 4.7/5 rating from 228 users (SaaSworthy, Jan 2026)
- Inline real-time visual editing — non-technical people never see Markdown or Git
- Setup takes ~10 minutes for a developer

### Access management & guest authors
- **Free tier:** Limited users, basic roles
- **Team plan:** More users, editorial roles
- **Enterprise:** Custom roles, SSO, SCIM provisioning, editorial workflows (branching + PR review before publishing)
- Guest authors are invited as users with "editor" or "author" roles (can edit content, not site config)
- Not as granular as WordPress's role system on free/team plans

### Pricing
- Free tier available for small teams
- Team and Enterprise plans for more users/roles
- See [tina.io](https://tina.io) for current pricing

Sources: [Smashing Magazine case study](https://www.smashingmagazine.com/2023/09/smashing-magazine-tinacms-manage-editorial-workflow/), [Bejamas review](https://bejamas.com/hub/headless-cms/tina), [StaticMania review](https://staticmania.com/blog/discover-the-power-of-tina-cms)

---

## Media & Rich Content in Astro

### YouTube embeds
- Dedicated [`astro-embed` component](https://astro-embed.netlify.app/components/youtube/) — lazy-loading, performant, no iframe bloat

### Images
- Built-in image optimization (resizing, WebP conversion, lazy loading)
- Cloudflare Media integration available (Jan 2026)

### Videos
- HTML `<video>` tags in Markdown
- MDX for custom video components

### Rich/interactive content
- MDX lets you embed React/Svelte/Vue components inside Markdown posts
- Interactive charts, code playgrounds, custom widgets — all possible
- Can configure `.md` files to use MDX features without renaming them

Sources: [Astro Embed](https://astro-embed.netlify.app/components/youtube/), [MDX in .md files](https://www.codestudy.net/blog/how-to-customize-markdown-with-astro-components/), [Astro Jan 2026 updates](https://astro.build/blog/whats-new-january-2026/)

---

## TinaCMS Media Storage

TinaCMS offers two approaches for handling uploaded media (images, videos, files):

### Option 1: Repo-based media (default)

- Media files get committed directly to your Git repo (e.g. `public/uploads/`)
- Max file size: 100 MiB via TinaCloud
- Supports images, videos, PDFs, and many other formats out of the box
- Good for images and small files
- **Not ideal for videos** - large files bloat the Git repo and slow down clones

### Option 2: External media providers (recommended for video)

TinaCMS has built-in support for external storage:

| Provider | Notes |
|----------|-------|
| **Cloudinary** | Best for images + video, has transcoding/optimization |
| **AWS S3** | Raw storage, cheap, flexible |
| **DigitalOcean Spaces** | S3-compatible, simple setup |

- Media is stored externally, only the URL reference goes in your Markdown/Git
- Requires a small backend handler (a single API route on Vercel/Netlify)
- The editor experience is the same - non-technical users upload through the TinaCMS media manager UI regardless of where files are stored

### Recommendation

Use repo-based media for images (simple, version-controlled). Use Cloudinary or S3 for video uploads to avoid bloating the Git repo.

Sources: [TinaCMS Media Overview](https://tina.io/docs/reference/media/overview/), [Repo-based Media](https://tina.io/docs/reference/media/repo-based/), [External Media Providers](https://tina.io/docs/reference/media/external/authentication/)

---

## Custom Design

Astro gives full design control:
- Write your own layouts and components, or start from a theme/template
- No locked-in design system
- Use Tailwind, vanilla CSS, or any CSS framework
- TinaCMS doesn't constrain design — it's just an editing layer on top
- Large collection of [Astro blog templates](https://statichunt.com/blog/best-astro-blog-templates) available as starting points

---

## Newsletters & Email

Separate from Astro + TinaCMS. Integrate a third-party service:

| Service | Notes |
|---------|-------|
| **ConvertKit** | Popular with bloggers, Astro integration guides available |
| **Buttondown** | Simple, Markdown-native newsletter tool |
| **Resend** | Developer-friendly email API |
| **RssFeedPulse** | Auto-sends new posts to subscribers via RSS |

Astro also has a prebuilt [Newsletter Kit theme](https://astro.build/themes/details/newsletter-kit/) with subscribe forms ready to connect to any provider.

Ghost has newsletters built-in; with Astro you wire it yourself (straightforward).

Sources: [ConvertKit + Astro guide](https://www.robcipolla.co.uk/blog/step-by-step-guide-to-building-a-custom-newsletter-subscription-form-with-astro-react-and-convertkit), [RssFeedPulse + Astro](https://www.rssfeedpulse.com/docs/howtos/astro)

---

## Hosting & Deployment

All free-tier options:

| Host | Notes |
|------|-------|
| **Vercel** | Best Astro integration, preview deployments, analytics |
| **Netlify** | Great DX, form handling, functions |
| **Cloudflare Pages** | Fast global CDN, generous free tier |
| **GitHub Pages** | Simplest, but fewer features |

Point `blog.eventual.ai` via DNS CNAME to your hosting provider. All support custom domains on free tiers.

---

## Alternatives Considered

### Static Site Generators

| SSG | Language | Build Speed | Best for | Drawback |
|-----|----------|-------------|----------|----------|
| **Astro** | JS/TS | Fast | Modern blogs, content sites | Newer ecosystem |
| **Hugo** | Go | Fastest (ms) | Massive sites (10k+ pages) | Go templates widely disliked |
| **11ty (Eleventy)** | JS | Fast | Tinkerers wanting max control | Bare-bones, build everything yourself |
| **Jekyll** | Ruby | Slower | Zero-config GitHub Pages | Declining, Ruby env annoying |
| **Zola** | Rust | Fast | Hugo-like without Go templates | Small ecosystem |
| **Gatsby** | JS/React | Slow | Avoid for new projects | Dependency hell, GraphQL overkill |

### CMS Options (for non-technical editors)

| CMS | Type | Content storage | Editor UX | Cost |
|-----|------|----------------|-----------|------|
| **TinaCMS** | Git-based | Markdown files in repo | Good (inline visual editing) | Free tier + paid |
| **CloudCannon** | Git-based | Markdown files in repo | Excellent (WordPress-like) | ~$45/mo |
| **Decap CMS** | Git-based | Markdown files in repo | Basic | Free (but unmaintained) |
| **Strapi** | Database-backed | Database (Postgres/SQLite/MySQL) | Moderate (form-based) | Free self-hosted |
| **Ghost** | Database-backed | Database | Excellent | Free self-hosted / $9+/mo hosted |

### Headless CMS (database-backed, content NOT in Git)

| CMS | Best for | Non-tech editors | Cost |
|-----|----------|-----------------|------|
| **Strapi** | Complex apps, custom content models | Moderate (steeper learning curve) | Free self-hosted, Cloud $$$+ |
| **Contentful** | Enterprise, best out-of-box editor UI | Excellent | Expensive |
| **Storyblok** | Visual editing, marketing teams | Excellent (best visual editor) | Paid |
| **Sanity** | Highly customizable studio | Good | Free tier + paid |
| **Ghost** | Blogging specifically | Excellent | Free self-hosted / $9+/mo |

### Self-hosted alternatives (from Reddit)

| Platform | Notes |
|----------|-------|
| **Ghost** | Gold standard writing UX, built-in newsletters. Now requires MySQL 8. |
| **WriteFreely** | Ultra-minimalist. ActivityPub/Mastodon integration (fediverse followers). |
| **Grav** | Flat-file CMS (no database). Has admin panel. Easy backup/migration. |
| **Publii** | Desktop app that generates static HTML. No online admin to hack. |

---

## Reddit Community Consensus (r/webdev, r/selfhosted, r/jamstack)

- **Astro** is "the new standard" — community energy is here for new blogs
- **Hugo** is unmatched for build speed on massive sites, but Go templates are a serious pain point
- **11ty** is loved by tinkerers, bare-bones by design
- **Jekyll** is declining, only recommended for simplest GitHub Pages path
- **Gatsby** is in sharp decline — "dependency hell," avoid for new projects
- **Zola** is a rising dark horse (Hugo speed + easier templates, small ecosystem)
- **Ghost** is the best self-hosted writing experience, but content is in a database, not Git

---

## Key Sources

- [Astro vs Next.js benchmarks — Senorit](https://senorit.de/en/blog/astro-vs-nextjs-2025)
- [Astro vs Next.js for blogs — Sourabh Yadav](https://sourabhyadav.com/blog/astro-vs-nextjs-for-blogs-2026/)
- [Astro 40% faster static site performance — BetterLink](https://eastondev.com/blog/en/posts/dev/20251202-astro-vs-nextjs-comparison/)
- [Smashing Magazine + TinaCMS case study](https://www.smashingmagazine.com/2023/09/smashing-magazine-tinacms-manage-editorial-workflow/)
- [TinaCMS review — Bejamas](https://bejamas.com/hub/headless-cms/tina)
- [TinaCMS review — StaticMania](https://staticmania.com/blog/discover-the-power-of-tina-cms)
- [Git-based headless CMS comparison — Statichunt](https://statichunt.com/blog/git-based-headless-cms)
- [Decap CMS alternatives — SitePins](https://sitepins.com/blog/decapcms-alternatives)
- [Top SSGs 2026 — Hygraph](https://hygraph.com/blog/top-12-ssgs)
- [Headless CMS comparison 2026 — Cosmic](https://www.cosmicjs.com/blog/headless-cms-comparison-2026-cosmic-contentful-strapi-sanity-prismic-hygraph)
- [Strapi vs Storyblok vs Contentful — Netguru](https://www.netguru.com/blog/strapi-vs-storyblok-vs-contentful)
- [Astro Embed components](https://astro-embed.netlify.app/components/youtube/)
- [Astro blog templates 2026 — Statichunt](https://statichunt.com/blog/best-astro-blog-templates)
- [Ghost + Markdown + Git discussions](https://forum.ghost.org/t/how-to-write-ghost-posts-in-markdown-and-track-via-git/17836)
- [WordPress Markdown Git plugin](https://github.com/nilsnolde/wordpress-markdown-git)
- [Hosted.md](https://hosted.md/)
