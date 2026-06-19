# HANDOFF — CPA Dashboard (Corporate Tax Study & Calculator)

_Last updated: 2026-06-19_

## Status
**LIVE.** Built fresh per spec, verified locally, deployed to Vercel, and wired into
app.fastinsights.io.

- **Standalone:** https://cpa-dashboard-ashy.vercel.app (HTTP 200) — Vercel project `cpa-dashboard`,
  team `jessica-dougherty-s-projects`, deploy `dpl_2E4SXodhQmeb55qpR8LHpu1uYsX4` (READY).
  (Plain `cpa-dashboard.vercel.app` is owned by someone else; the prod alias is `-ashy`.)
- **Hub:** https://app.fastinsights.io/cpa-dashboard/ (HTTP 200; bare `/cpa-dashboard` 307→ slash).
  Tile "Corporate Tax Study" (Scale icon) on the landing picker. Wired in the `fast-insights-app`
  repo (commit `af3bea4`): `src/pages/Landing.tsx` tile + `vercel.json` redirect/rewrite proxy.

## What it is
Single-file (`index.html`) CPA study guide + planning calculator. Hash-routed, 4 top tabs / 5 pages:
`#/corporation/nol-section-382`, `#/corporation/consolidations`, `#/property/property-for-stock-sh`,
`#/dividend/accumulated-current-ep`, `#/liquidation/stock-gain-loss`. See README.md / CLAUDE.md.

## Verification (2026-06-19, local preview port 8740 — no console errors, mobile 390px no overflow)
Every spec acceptance number passed:
- **§382:** annual limit **$365,000** (accepts rate `3.65` or `0.0365`); unused-NOL 1/5/10/20yr =
  **$4,635,000 / $3,175,000 / $1,350,000 / $0**; tracker flags "Possible ownership change" at 60 pts.
- **Consolidations:** Press/Swank **$109,000**; intercompany land deferred **$40,000** / recognized
  **$0**; inventory+land eliminations **$420,000**; Ex1 $20,000 & AB Group $6,000 worked examples.
- **Property (§351):** Dole recognized **$40,000** (AR $100k, realized $65k, boot $40k); transferor
  control **90%**; Carr realized **$10,000** / recognized **$0**; Lind/Ace corp basis **$40,000**.
- **Dividend E&P:** total E&P **$50,000**, dividend **$50,000**, ROC **$25,000**, capital gain
  **$5,000**, ending basis **$0**.
- **Liquidation:** corporation-level gain **$50,000**, character **capital**.
- Copy/export, answer-card reveal, FAQ, advisor checklist, validation all working.

## Deploy / redeploy
- **Standalone:** `npx vercel --prod --scope jessica-dougherty-s-projects` from this folder.
  (NOT git-connected yet — see below.)
- **Hub tile/proxy:** edit `fast-insights-app` (Landing.tsx + vercel.json) → `git push origin main`
  (auto-deploys). `npm run build` first to catch TS errors (TS strict is the gate).

## Open follow-ups (optional)
- **GitHub + git-connect:** this repo has a LOCAL git history (initial commit) but **no GitHub repo
  yet** — `gh` isn't logged in on this machine, so the repo couldn't be created from CLI. To enable
  push-to-deploy like the other tools: create `github.com/jessiesfaith/cpa-dashboard`, then
  `git remote add origin github-jessica:jessiesfaith/cpa-dashboard.git`, `git push -u origin main`,
  and `vercel git connect` (gotcha: the SSH-alias remote can't be parsed — temporarily set origin to
  the https URL, connect, then restore the alias). Until then, redeploy via the CLI command above.
- Consider a short "what's missing" review of the worked-example prose for edge phrasing.

## Notes
- Built via a verify-workflow (5 page modules generated + adversarially traced against acceptance
  numbers, then integrated). Build artifacts (`_build_pages.workflow.js`, `_inject.js`) were removed.
- Existing tools/pages untouched. Only additive changes to the hub (new tile + proxy).
