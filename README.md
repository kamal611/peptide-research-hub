# PeptideAAA

A static, 100+ article site covering peptide research, news, guides, safety, and comparisons across 6 categories.

## What's in here (flat structure — no subfolders)

Every page lives directly in this folder — `index.html`, `category-*.html`, `article-*.html`, `disclaimer.html`, `style.css`, `server.js`, `package.json`, `404.html`, `sitemap.xml`, `robots.txt`. This is intentional: a flat structure uploads reliably through GitHub's web "Upload files" flow (including from a phone), where nested folders sometimes get dropped.

## Updating the GitHub repo (replace everything)

1. On github.com, open the repo and delete any existing files (select all → delete, or delete one by one if there are only a few).
2. Click **Add file → Upload files**, then select/drag *all* the files from this folder at once (there are no subfolders to worry about).
3. Commit. Railway will redeploy automatically since it's connected to this repo.

## Deploying to Railway

Already connected: this repo is linked to a Railway project, which auto-deploys on every push to `main` via `npm start` (`node server.js`). No build step — it's a static app served by a small Node file server.

## Updating content later

The article text lives in `content/*.json` in the site's source project (not included in this deploy bundle), rendered through `build.py`. Re-run `python3 build.py` after editing content, then re-upload this `build/` folder's contents.

## Branding

The logo is embedded directly in every page as a base64 data URI (in the `<head>`/header markup), so there's no separate image file to keep track of or that can go missing on upload.

## Before this goes live for real

This content is written to explain what's approved vs. not, and to avoid giving dosing/sourcing instructions for unapproved compounds — worth keeping that framing intact if you or anyone else edits it, both for accuracy and to avoid regulatory/liability exposure. Consider having a licensed professional review before this is used to inform real medical decisions at scale (e.g. if you monetize with affiliate links to peptide vendors, ad networks and payment processors increasingly scrutinize this niche specifically).
