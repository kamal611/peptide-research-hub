# Peptide Research Hub

A static, 100-article site covering peptide research, news, guides, safety, and comparisons across 6 categories (Basics, News, Guides, Research, Safety, Comparisons & Q&A).

## What's in here

- `index.html` — homepage
- `category/*.html` — 6 category listing pages
- `articles/*.html` — the 100 individual articles
- `disclaimer.html` — medical/legal disclaimer (linked in every page's footer)
- `style.css` — single stylesheet, mobile-responsive, light/dark-friendly
- `sitemap.xml`, `robots.txt` — basic SEO plumbing
- `server.js`, `package.json` — a minimal zero-dependency Node static server for deployment

## Deploying to Railway

**Option A — GitHub (recommended):**
1. Push this folder to a new GitHub repo.
2. In Railway, click **New Project → Deploy from GitHub repo** and select it.
3. Railway detects Node via `package.json` and runs `npm start` automatically (which runs `node server.js`). No build step needed — it's already a static build.
4. Once deployed, Railway gives you a `*.up.railway.app` URL. Attach a custom domain under the service's **Settings → Domains** if you have one.

**Option B — Railway CLI:**
```bash
npm i -g @railway/cli
railway login
railway init
railway up
```
Then `railway domain` to get a public URL.

## Updating content later

The actual article text lives in `content/*.json` in the site's source project (not included in this deploy bundle), rendered through `build.py`. To change an article's wording, category, or add new ones, that's the place to edit — this `build/` folder is generated output. If you don't have the source project, the fastest path to edit copy is directly in each `articles/*.html` file (inside the `<div class="article-body">` block) and re-deploy.

## Before this goes live for real

This content is written to explain what's approved vs. not, and to avoid giving dosing/sourcing instructions for unapproved compounds — worth keeping that framing intact if you or anyone else edits it, both for accuracy and to avoid regulatory/liability exposure. Consider having a licensed professional review before this is used to inform real medical decisions at scale (e.g. if you monetize with affiliate links to peptide vendors, ad networks and payment processors increasingly scrutinize this niche specifically).
