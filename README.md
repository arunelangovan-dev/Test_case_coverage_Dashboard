# Heartland non-digital onboarding — coverage dashboard

A static dashboard (`index.html`) driven by a data file (`data.json`). Update
the data file, push, and the live site reflects it — no rebuild step needed.

## 1. Get it on GitHub Pages (5 minutes, manual updates)

1. Create a new repo (public or private — Pages works on both, private needs
   GitHub Pro/Team/Enterprise) and push these files to it.
2. In the repo: **Settings → Pages → Build and deployment → Source: Deploy
   from a branch → Branch: `main`, folder: `/ (root)`**. Save.
3. GitHub gives you a URL like `https://<your-username>.github.io/<repo>/`
   within a minute or two.
4. To refresh the numbers later: edit `data.json` (by hand, or paste in new
   counts), commit, push. The live page updates automatically — GitHub Pages
   redeploys on every push to the branch you picked.

That's the whole loop if you're fine updating `data.json` yourself after each
testing cycle.

## 2. Fully automatic refresh (optional)

`.github/workflows/update-coverage.yml` runs on a daily schedule (or on
demand from the **Actions** tab), re-pulls the Confluence page, and commits a
fresh `data.json` if anything changed — so the site updates itself with no
manual step.

To wire it up:

1. Create an [Atlassian API token](https://id.atlassian.com/manage-profile/security/api-tokens)
   for whichever account should read the page.
2. In the repo: **Settings → Secrets and variables → Actions → New repository
   secret**, and add:
   - `CONFLUENCE_BASE_URL` — e.g. `https://constantinople.atlassian.net/wiki`
   - `CONFLUENCE_EMAIL` — the Atlassian account email tied to the token
   - `CONFLUENCE_TOKEN` — the API token from step 1
3. Check `scripts/update-data.mjs` — the parsing logic is written for this
   page's current layout (a heading ending in "Test Cases" per section, a
   "Bug Summary" table, PASS/FAIL/NA status macros). If your page's structure
   differs, or changes later, that's the file to adjust — the auth/fetch part
   above it shouldn't need to change.
4. Push. The workflow will run on its schedule, or trigger it immediately
   from **Actions → Refresh coverage data → Run workflow**.

Treat the script as a working starting point rather than a finished parser —
Confluence's storage format has enough edge cases (merged cells, nested
macros) that it's worth a test run against the real page before trusting it
unattended.

## Files

- `index.html` — the dashboard. Self-contained; only dependency is Chart.js
  from a CDN.
- `data.json` — the data the dashboard reads. This is the file to edit or
  regenerate.
- `scripts/update-data.mjs` — pulls Confluence and rewrites `data.json`.
- `.github/workflows/update-coverage.yml` — schedules the script to run and
  commits the result.
