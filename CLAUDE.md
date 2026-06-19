# CPA Dashboard (Corporate Tax Study & Calculator) -- repo notes

Generic workflow rules live in `~/.claude/CLAUDE.md` (they apply to every repo). This file holds
**repo-specific facts only**. Source of truth = this repo, not the chat transcript.

## Repo facts (created 2026-06-19)
- **What:** Single-page static CPA study guide + planning calculator for four corporate-tax
  topics: Corporation (NOL Section 382, Consolidations), Property (Property for Stock - SH),
  Dividend (Accumulated & Current E&P), Liquidation (Stock-Gain/Loss). All client-side; no accounts.
- **Package manager / framework:** none. Vanilla HTML/CSS/JS, no `package.json`, no build step.
- **Key file:** `index.html` -- the entire app (inlined CSS + JS).
- **Architecture:** a small framework (hash router `#/<top>/<sub>`, top/sub-tab nav, shared `U`
  helper object, auto-rendered disclaimer + Copy-summary/Print) plus five **page modules**. Each
  page self-registers via `registerPage({top,topLabel,sub,subLabel,title,html,init,summary})`. Add a
  page by appending another `registerPage(...)` `<script>`. Shared CSS classes + `U` API are
  documented in comments at the top of `index.html`. IDs/helpers per page are prefixed
  (`n382_`, `cons_`, `pfs_`, `ep_`, `liq_`) to avoid collisions.
- **Dev (localhost):** `python -m http.server 8000` -> http://localhost:8000 (or `npx http-server`).
  Preview config name `cpa-dashboard`, port 8740.
- **Build / test / lint / typecheck:** none configured. Verification = manual + the worked-example
  acceptance numbers (each page prefilled to a known answer; see HANDOFF.md).
- **Deploy:** Vercel static (`vercel.json` -> `cleanUrls`). CLI: `npx vercel --prod
  --scope jessica-dougherty-s-projects`. (Git-connect later if desired -- see HANDOFF.md.)
- **Production:** https://cpa-dashboard-ashy.vercel.app + app.fastinsights.io/cpa-dashboard tile/proxy.
  Vercel project `cpa-dashboard` (team `jessica-dougherty-s-projects`). NOTE: plain
  `cpa-dashboard.vercel.app` is owned by someone else, so the prod alias is `cpa-dashboard-ashy`.

## GitHub / accounts (Jessica)
- This is **Jessica's** ecosystem (like the other Fast Insights tools). Vercel CLI on this machine
  is logged in as `jessicadougherty4321-6324` (team `jessica-dougherty-s-projects`).
- GitHub pushes for Jessica's repos use the SSH host alias **`github-jessica`** (key
  `~/.ssh/jessiesfaith_ed25519`); remote pattern `github-jessica:jessiesfaith/<repo>.git`.
  The machine's stored HTTPS credential is Chris's (`dogsleddev`) and CANNOT push to Jessica's repos.
- `gh` CLI is NOT logged in on this machine, so a new GitHub repo must be created on github.com (or
  via an authenticated `gh`) before `git push` works.

## Environment notes (this machine)
- **OneDrive Files-On-Demand:** this repo is inside a OneDrive-synced vault; files may be cloud
  placeholders that intermittently fail to read. Retry once; `git` reads fine.
