---
title: "Blog setup for eventual.ai"
description: "How we decided to set up the Eventual blog using Astro + TinaCMS on a subdomain."
pubDate: "Feb 22 2026"
heroImage: "../../assets/blog-placeholder-5.jpg"
---


> Decision document for how to integrate an Astro + TinaCMS blog with the existing eventual.ai Next.js monorepo.

## Context

- `eventual.ai` runs on Next.js (in `apps/eventual` of the monorepo)
- The existing `daft.ai/blog` uses Payload CMS within the same monorepo
- The eventual.ai blog should be **new infrastructure** using Astro + TinaCMS, separate from the Payload CMS setup

## Decision: subdomain (`blog.eventual.ai`)

### Why subdomain over subdirectory

The main site is Next.js. The blog is Astro. Mixing them via subdirectory would require Vercel rewrites to proxy `/blog/*` from the Next.js project to a separate Astro deployment. This adds fragility (headers, assets, caching, path prefixes) for a modest SEO benefit.

**Subdomain tradeoffs:**

| | Subdomain (`blog.eventual.ai`) | Subdirectory (`eventual.ai/blog`) |
|---|---|---|
| **Setup** | DNS CNAME + deploy. Done. | Vercel rewrite proxy between two frameworks |
| **Independence** | Fully independent deploys | Coupled - proxy config can break |
| **SEO** | Slightly worse (Google may treat as semi-separate) | Better (link equity flows to main domain) |
| **Maintenance** | Zero coupling with Next.js app | Must maintain rewrite rules as both apps evolve |

The SEO difference is modest for a company blog where traffic comes primarily from social shares, newsletters, and direct links rather than organic search. Many companies use blog subdomains successfully (GitHub uses `github.blog`, for example).

If SEO becomes critical later, migrating from subdomain to subdirectory is straightforward with 301 redirects.

### Setup steps

1. Create the Astro + TinaCMS project in its own repo
2. Deploy to Netlify / Vercel / Cloudflare Pages (all free tier)
3. Add DNS CNAME: `blog.eventual.ai` -> hosting provider
4. Add a link from the eventual.ai nav to `blog.eventual.ai`
