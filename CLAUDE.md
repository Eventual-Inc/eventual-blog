# Eventual blog

Astro-based blog for eventual.ai, served at `eventual.ai/blog` via rewrites from the Next.js monorepo.

## Architecture

- This repo is the Astro blog, deployed as its own Vercel project (`eventual-blog-2.vercel.app`)
- The monorepo (`eventual-monorepo`, branch `blog-rewrite`) rewrites `eventual.ai/blog/*` to this deployment
- Static assets (`/_astro/*`, `/fonts/*`, `/eventual-logo.svg`) also have rewrites in the monorepo's `next.config.ts`
- No `base` path config in Astro - the blog pages live at `/blog/` naturally because of the `src/pages/blog/` directory structure

## Adding a new blog post

1. Create a new `.md` (or `.mdx`) file in `src/content/blog/`
2. Add frontmatter:
   ```md
   ---
   title: 'Post title'
   description: 'Short description'
   pubDate: 'Feb 24 2026'
   heroImage: '../../assets/my-image.jpg'
   ---
   ```
3. Drop the hero image in `src/assets/` - it goes through Astro's image pipeline and is served from `/_astro/` (already covered by monorepo rewrites)
4. Write content in Markdown below the frontmatter
5. Commit and push - Vercel auto-deploys

## Important notes

- Images must go in `src/assets/`, not `public/`. Assets in `public/` would need new rewrites added in the monorepo.
- The Vercel project watches `eventual-blog-2` on GitHub (a clone). Push to both remotes:
  - `origin` - `Eventual-Inc/eventual-blog`
  - `blog2` - `Eventual-Inc/eventual-blog-2`
- MDX support is enabled for posts that need embedded components
