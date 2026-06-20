# HANDOFF — CPA Dashboard (Corporate Tax Study & Calculator)

_Last updated: 2026-06-20_

## Status
**LIVE.** Single-file study guide + planning calculator, deployed to Vercel and wired into
app.fastinsights.io. Current deployed HEAD: **`b82682f`** (worked-math tables + collapse-by-default).

- **Standalone:** https://cpa-dashboard-ashy.vercel.app — Vercel project `cpa-dashboard`, team
  `jessica-dougherty-s-projects`. (Plain `cpa-dashboard.vercel.app` is owned by someone else; the
  prod alias is `-ashy`.)
- **Hub:** https://app.fastinsights.io/cpa-dashboard/ — tile "Corporate Tax Study" (Scale icon) on
  the landing picker, wired in the `fast-insights-app` repo (`src/pages/Landing.tsx` tile +
  `vercel.json` redirect/rewrite proxy).

## What it is
Single-file (`index.html`) CPA study guide **and** planning calculator with **Excel-style
step-by-step worked-math tables**. Hash-routed, 4 top tabs / 5 pages:
`#/corporation/nol-section-382`, `#/corporation/consolidations`, `#/property/property-for-stock-sh`,
`#/dividend/accumulated-current-ep`, `#/liquidation/stock-gain-loss`. See README.md / CLAUDE.md.

## What's new (2026-06-20)
Two features were added across all five calculators (verified locally; no console errors):

### 1. Excel-style "worked-math" tables — **9 tables total** (commit `a45b080`)
Every calculator now renders a spreadsheet-style table that shows **every step of the math with no
steps skipped** (a teaching request), with two states:
- **Blank fields → example.** Each blank input falls back to an example default, rendered
  grey-italic (`.exq`), and the header shows a **📘 Example values** badge.
- **Type a number → live.** It recomputes instantly and the badge flips to **✏️ Your numbers**.

Shared helpers (in the framework `<script>`):
- `U.mtable(def)` — builds the table from `{title, subtitle, mode, rows[], foot}`; row `kind` is
  `sect` (step header) / `in` (addend) / `sub` (subtotal) / `result` (accent answer). Renders as a
  collapsible `<details class="mwrap">`.
- `U.fval(n, isExample)` — formats a $ amount, wrapping example-sourced values in `.exq`.
- `U.setMath(host, def)` — renders a table into `host` **and preserves its open/closed state across
  re-renders** (the calculators rebuild the table on every keystroke; without this it would slam
  shut while you type). It reads `prev.open` → `def.open`; it does **not** recurse.

Per page (container ids + example-default constant):

| Page (route) | Worked-math tables | Container ids | Example constants |
|---|---|---|---|
| NOL §382 (`/corporation/nol-section-382`) | 1 | `n382_math` | `N382_EX` |
| Consolidations (`/corporation/consolidations`) | 3 | `cons_math`, `cons_psMath`, `cons_elMath` | `CONS_PS_EX`, `CONS_EL_EX` (main table compares the member grid to `cons_PRESS_SWANK` / `cons_AB_GROUP`) |
| Property for Stock (`/property/property-for-stock-sh`) | 2 | `pfs_m1_math`, `pfs_m3_math` | `PFS_M1_EX`, `PFS_M3_EX` |
| E&P / Dividend (`/dividend/accumulated-current-ep`) | 1 | `ep_math` | `EP_EX` |
| Liquidation (`/liquidation/stock-gain-loss`) | 2 | `liq_corpMath`, `liq_shMath` | `LIQ_CORP_EX`, `LIQ_SH_EX` |

Notes: PFS **Calculator 2** (transferor-group table) is itself an editable grid and intentionally
has no worked-math table. The added tables are **additive** to the existing bignum outputs and
copy-summary (those were left intact). Container-id naming mixes `_math` and `Math` suffixes
(cosmetic only).

### 2. Collapse-by-default (commits `3ad421e`, `19f5cf6`, `1696849`, `b82682f`)
To keep the long study pages short, these now render **collapsed** and expand on a ▸ click:
- **Examples / Worked example / Check-your-understanding / Formulas** cards — folded by the
  `collapseExamples(scope)` pass in `render()`. It matches `.grp-tag` labels (`/^example/`,
  `worked example`, `check your understanding`, `/formula/`) and also folds **headerless formula
  intro boxes** (a card whose first child is a `.formula` with a `.ftitle`).
- **FAQ** and **Review checklist** — `U.faq()` / `U.checklist()` now wrap their output in a closed
  `<details class="collapse">` with a count badge.
- **Worked-math tables** — collapsed by default (see §1).

⚠️ **Intentional behavior to know:** because the worked-math tables default **collapsed**, the
step-by-step teaching view is hidden until the user clicks the table header on first load. This is
per the "collapse worked math" request. To make it teaching-first instead, pass `open:true` on the
first render (or open the first table per page) — one small change in the `*RenderMath` call sites /
`U.setMath`.

## Verification
**2026-06-20 (local preview, port 8740 — no console errors):** worked-math tables render the example
numbers by default (📘 badge), recompute live and flip to ✏️ when any field is edited, blank fields
fall back to grey-italic example values, an opened table **stays open while typing**, and every
Example / Worked example / Check-your-understanding / Formulas / FAQ / checklist / worked-math
section starts collapsed and expands correctly. Adversarial code audit (full-file read + HTML↔JS id
cross-check): no orphaned references, no recursion, all 5 `summary()` functions valid, no dead code.

**2026-06-19 acceptance numbers (still pass):**
- **§382:** annual limit **$365,000** (rate accepts `3.65` or `0.0365`); unused-NOL 1/5/10/20yr =
  **$4,635,000 / $3,175,000 / $1,350,000 / $0**; tracker flags an ownership change at 60 pts.
- **Consolidations:** Press/Swank **$109,000**; intercompany land deferred **$40,000** / recognized
  **$0**; inventory+land eliminations **$420,000**.
- **Property (§351):** Dole recognized **$40,000** (AR $100k, realized $65k, boot $40k); control
  **90%**; Carr realized **$10,000** / recognized **$0**; Lind/Ace corp basis **$40,000**.
- **Dividend E&P:** total E&P **$50,000**, dividend **$50,000**, ROC **$25,000**, capital gain
  **$5,000**, ending basis **$0**.
- **Liquidation:** corporation-level gain **$50,000** (capital); shareholder-level gain **$60,000**.

## Deploy / redeploy
- **GIT-CONNECTED — push-to-deploy.** Repo `github.com/jessiesfaith/cpa-dashboard` (push via SSH
  alias `github-jessica`). `git push origin main` → Vercel auto-deploys to production. Manual
  fallback: `npx vercel --prod --scope jessica-dougherty-s-projects`.
  - `vercel git connect` gotcha (already done): it can't parse the `github-jessica:` SSH-alias
    remote — temporarily `git remote set-url origin https://github.com/jessiesfaith/cpa-dashboard.git`,
    connect, then restore the alias (`github-jessica:jessiesfaith/cpa-dashboard.git`).
- **Hub tile/proxy:** edit `fast-insights-app` (Landing.tsx + vercel.json) → `git push origin main`
  (auto-deploys). `npm run build` first to catch TS errors (TS strict is the gate).

## Open follow-ups (optional)
- **Decide the worked-math default:** currently collapsed on load. If teaching-first is preferred,
  flip the first render to `open:true` (see §2 warning).
- **Consolidations main table (`cons_math`)** is the one worked-math table without the per-cell
  grey-italic "example default" affordance — it's driven by the member-grid totals and shows its
  badge via `cons_isExample(rows)` (grid vs. sample datasets). Fine as-is; noted for consistency.

## Notes
- Built via a verify-workflow originally (5 page modules, adversarially traced against acceptance
  numbers). 2026-06-19 cleanup (commit `7b1d2ee`) removed dead CSS + the unused `fmtUSDc` helper.
- This session replaced the old static `.calc` "worked solution" blocks on NOL / E&P / Liquidation
  with the new worked-math tables (old ids `n382_calc1/2`, `ep_c*`, `liq_corpCalc/shCalc` fully
  removed from both HTML and JS — verified no orphans). On **Consolidations** the worked-math tables
  were **added alongside** the existing `.calc` breakdown + `cons_o*` spans (those remain, still used
  by the calculator output panel and `summary()`).
- Existing tools/pages untouched. Hub changes were additive (tile + proxy).
