# PeptideAAA — Automation Playbook

This file is the persistent "memory" for the daily article automation. Read it first, every run.

## System overview

- **Site:** PeptideAAA — peptide/GLP-1 research, news, guides, safety, comparisons.
- **Repo:** github.com/kamal611/peptide-research-hub, branch `main`, FLAT file structure (no folders except `_automation/`).
- **Hosting:** Railway project "gleaming-encouragement", service "peptide-research-hub" — auto-deploys on every push to `main`.
- **Live URL:** https://peptideaaa.com (custom domain; the Railway-generated URL https://peptide-research-hub-production.up.railway.app also still resolves to the same deployment)
- **Manifest:** `_automation/manifest.json` in the repo lists every published article (slug, title, category). ALWAYS fetch and read this first to avoid duplicate topics, and update it after publishing new ones.
- **Daily volume:** 5-10 new articles per run.

## How publishing works (no git access from the cloud sandbox)

The cloud sandbox cannot push to GitHub directly (blocked by network policy). Instead, publishing goes through a **linked device's browser** (Claude_Browser tools via the remote-devices bridge):

1. Confirm a device is linked (try `get_device_info`; if it fails, no computer is linked this session — tell the user you need their computer linked again before you can publish, and stop there).
2. `Claude_Browser__navigate` to `https://github.com/kamal611/peptide-research-hub/new/main` to create a new file, or `https://github.com/kamal611/peptide-research-hub/edit/main/<filename>` to edit an existing one. If access is refused, call `Claude_Browser__request_access` with scope "site" and retry.
3. Use `Claude_Browser__find` to locate the filename field and the editor, `Claude_Browser__form_input` / `Claude_Browser__computer` (click + type) to fill them in. IMPORTANT when editing an existing file: click inside the actual text first, then `ctrl+a` then `Delete` to confirm the editor is truly empty (verify with a screenshot) before typing the replacement — ctrl+a followed directly by typing can silently fail to select existing content and instead prepend new text above the old, corrupting the file.
4. Click "Commit changes..." and confirm in the dialog that appears. If no dialog appears after clicking, re-find the button and click again, or scroll up — do not assume the commit happened without seeing the confirmation dialog or the resulting file page.
5. After pushing, verify via the Railway MCP tools (`list-deployments`, `environment-status`) that a new deployment succeeded. Project ID `5b60b5ac-51fb-428e-859c-9bd79457eba2`, service ID `f6057504-b9aa-4f7d-9d08-61576284f38a`, environment ID `eccb4bc1-c2ac-472a-883f-b14125ade740`.

## File naming (flat structure — never use subfolders for site pages)

- New articles: `article-<slug>.html`
- Category pages (must be UPDATED, not created, when adding an article): `category-guides.html`, `category-news.html`, `category-research.html`, `category-safety.html`, `category-comparisons.html`, `category-basics.html`
- `sitemap.xml` should also be updated with new `<url>` entries, in this format (domain is `https://peptideaaa.com/`, not `example.com`):
```xml
  <url>
    <loc>https://peptideaaa.com/article-<slug>.html</loc>
    <lastmod>YYYY-MM-DD</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.7</priority>
  </url>
```
Use the actual publish date (today, in the run) as `<lastmod>`. Do not add `404.html` to the sitemap.

## Design system / page template

Every page shares the same shell: sticky header with the PeptideAAA logo (embedded as a base64 PNG data URI — copy it byte-for-byte from an existing page's `<img class="brand-logo" src="data:image/png;base64,...">` rather than retyping), a nav bar linking the 6 category pages, `<link rel="stylesheet" href="style.css">`, and a footer with categories/site links + disclaimer. **To get the exact template and logo data URI, fetch the raw HTML of an existing page** (e.g. navigate the browser to `https://github.com/kamal611/peptide-research-hub/raw/main/article-bpc-157-explained-what-the-research-says-about-the-body-protection-compound.html` and read the full page text) and copy its structure, header, and footer verbatim.

**As of the September 2026 SEO pass, every page's `<head>` also carries: a Google Search Console verification meta tag, the Google Analytics 4 `gtag.js` snippet (`G-0DJP0WZYMN`), Open Graph + Twitter Card meta tags, and a JSON-LD `<script type="application/ld+json">` block; every article also has a `<meta name="author" content="Mike Allen">` tag and a visible byline with a published date right after the `<h1>` (see `.byline` in `style.css`). When copying an existing article as a template for a new one, copy ALL of this verbatim — it does not change per article — but you MUST personalize these fields for the new article, or the new page will carry the old article's metadata:**
- `<title>`, `<meta name="description">`, `<link rel="canonical">` (path only, domain stays `https://peptideaaa.com/`)
- Breadcrumb link + `<span class="pill">` category
- `<h1>` and the full `<div class="article-body">` content
- `<meta property="og:title">`, `<meta property="og:description">`, `<meta property="og:url">`
- `<meta name="twitter:title">`, `<meta name="twitter:description">`
- Inside the JSON-LD block: `headline`, `description`, and `mainEntityOfPage.@id` (all three must match the new article's own title/description/URL — do NOT leave the copied article's values in place)

**Every new article MUST get today's actual date (the date of that automation run) — this applies to every article published, including daily news items, not just the flagship guides:**
- In the JSON-LD block, set `"datePublished"` and `"dateModified"` to today's date in full ISO 8601 with the site's timezone offset, e.g. `"2026-09-22T09:00:00-04:00"` — both fields get the same value at creation time. Insert them immediately after `"headline"`:
  ```json
  "headline": "...",
  "datePublished": "2026-09-22T09:00:00-04:00",
  "dateModified": "2026-09-22T09:00:00-04:00",
  "description": "...",
  ```
- In the visible byline right after `<h1>`, use this exact markup (swap in the real date twice — the `datetime` attribute in `YYYY-MM-DD` and the human-readable text):
  ```html
  <p class="byline"><span class="byline-name">By Mike Allen</span><span class="byline-dot">&bull;</span><span>PeptideAAA</span><span class="byline-dot">&bull;</span><time datetime="2026-09-22">September 22, 2026</time></p>
  ```
- If an already-published article is later substantively edited (correcting facts, expanding content), update only `dateModified` (and the visible date, if the page displays an "Updated" variant) — leave `datePublished` as the original publish date.

The author is always `Mike Allen` (Person) and the publisher is always `PeptideAAA` (Organization) in the JSON-LD `author`/`publisher` fields — leave those two blocks unchanged.

Article body markup: `<h2>` section headings, `<p>` paragraphs, wrapped in `<div class="article-body">`.

Article card markup (used on category pages and homepage) — insert one of these per new article inside the `<div class="card-grid">...</div>` on the relevant category page:
```html
<a class="card" href="article-<slug>.html">
  <span class="card-cat">ICON CATEGORY LABEL</span>
  <h3>Title</h3>
  <p>Meta description / 1-sentence summary.</p>
  <span class="card-arrow">Read the guide &rarr;</span>
</a>
```

## Editorial rules (must follow for every article — non-negotiable)

- Educational / research-summary tone, never medical advice.
- State each compound's real FDA-approval / regulatory status plainly and accurately.
- Clearly separate animal/preclinical evidence from human clinical evidence.
- No dosing, self-administration, or vendor/sourcing instructions.
- Include a "talk to a doctor" callout where relevant.
- Any patient/anecdotal content must be explicitly labeled illustrative/composite, not a real person.
- Every article is freshly written in the site's own voice — summarize and explain, never copy source text verbatim.
- Ground every "news"-labeled article in a REAL, verifiable current event/study/FDA action found via WebSearch — never fabricate a specific study, trial, or regulatory action. Genuine dated news will NOT exist in large volume every day — that's expected. Fill the rest of each day's batch with original evergreen guides/comparisons/safety/research-explainer articles on angles not already in the manifest, and don't label those "news."
- Length target: 500-1000 words.

## Daily automation contract (what each scheduled run should do)

1. Fetch `_automation/manifest.json` from the repo (via browser, raw file view) to see what's already published.
2. WebSearch for genuine recent peptide/GLP-1/FDA news. Use any real, verifiable stories found; fill the remainder of the day's 5-10 articles with evergreen topics not already in the manifest (avoid duplicating existing slugs/titles/angles).
3. Write each article per the editorial rules above, matching the site's template exactly.
4. Publish each new `article-<slug>.html` via the browser (create file flow).
5. Update the relevant `category-<cat>.html` page(s) to insert the new card(s) (edit file flow — fetch current content, insert the card HTML into the card-grid, resubmit, and double-check no duplicate/corrupted content resulted).
6. Update `sitemap.xml` with the new URLs (edit file flow).
7. Update `_automation/manifest.json` with the newly published articles (edit file flow).
8. Verify the Railway deployment succeeded (Railway MCP tools).
9. Tell the user what was published, with links, noting how many were genuine news vs. evergreen (or via push notification if unattended).

If no device is linked when a scheduled run fires, do not attempt any of the above — just let the user know publishing is blocked until their computer is linked again.
