# CPA Dashboard (Corporate Tax Study & Calculator) -- repo notes

Generic workflow rules live in `~/.claude/CLAUDE.md` (they apply to every repo). This file holds
**repo-specific facts only**. Source of truth = this repo, not the chat transcript.

## Repo facts (created 2026-06-19)
- **What:** Single-page static CPA study guide + planning calculator for five corporate-tax
  topics: Corporation (NOL Section 382, Consolidations), Property (Property for Stock - SH),
  Dividend (Accumulated & Current E&P), Liquidation (Stock-Gain/Loss), and **Distributions**
  (Nonliquidating vs. Liquidating, by entity — added 2026-07-06). All client-side; no accounts.
- **Package manager / framework:** none. Vanilla HTML/CSS/JS, no `package.json`, no build step.
- **Key file:** `index.html` -- the entire app (inlined CSS + JS).
- **Architecture:** a small framework (hash router `#/<top>/<sub>`, top/sub-tab nav, shared `U`
  helper object, auto-rendered disclaimer + Copy-summary/Print) plus six **page modules**. Each
  page self-registers via `registerPage({top,topLabel,sub,subLabel,title,html,init,summary})`. Add a
  page by appending another `registerPage(...)` `<script>`. Shared CSS classes + `U` API are
  documented in comments at the top of `index.html`. IDs/helpers per page are prefixed
  (`n382_`, `cons_`, `pfs_`, `ep_`, `liq_`; Distributions uses six: `dpn_`/`dpl_` partnership,
  `dcc_`/`dcl_` C-corp, `dsc_`/`dsl_` S-corp — operating vs liquidating) to avoid collisions.
- **Distributions module (2026-07-06):** one page (`#/distributions/nonliquidating`) laid out as a
  **two-column comparison** — nonliquidating (operating) LEFT, liquidating RIGHT — via a scoped
  `.dcmp-grid` (2 equal cols, stacks below 921px). Rules / worked-example / calculator cards are
  paired row-by-row for Partnership / C-corp / S-corp; a full-width "PARTNER PRESET" bar loads a JKL
  partner (Janet/Karen/Lisa, `dpn_preset`) into BOTH partnership calcs at once. Losses print in
  accounting parens via `par()`. **Side-by-side alignment** (Jessica cares a lot about this): fixed
  `table-layout` columns so labels wrap identically both sides; `U.mtable` now emits `data-key` per
  row; corresponding rows across a pair share a key (`p_*`/`c_*`/`s_*`, incl. section headers by step
  number); `alignMathPair()` (called at end of `recompute()`, on details-toggle, after
  `document.fonts.ready`) walks the keyed rows and inserts pixel-exact `tr.mpad` spacers so every
  matching row + numbered step badge lines up, with gaps only where a step is one-sided. Result-box
  values are bottom-aligned (flex) so each `$` sits on one line. **Verify alignment via
  `getBoundingClientRect` at ≥1440px width** (preview viewport often defaults narrow — resize first;
  screenshots time out on this heavy page).
- **Worked-math tables (2026-06-20):** every calculator renders an Excel-style step-by-step table via
  `U.mtable(def)` (builder) + `U.fval(n,isExample)` (example-muted `$`) + `U.setMath(host,def)`
  (renders into host **and preserves open/closed state** across the per-keystroke re-render). Each
  page has a `*_EX` example-default constant + `*_isLive()` mode check so blank fields show the
  example (📘 badge) and typed fields go live (✏️ badge). 9 original tables + 6 in Distributions;
  HANDOFF.md has the per-page id/constant map. NOL/E&P/Liquidation REPLACED their old `.calc` "worked solution" blocks;
  Consolidations ADDED tables alongside the existing `.calc` breakdown (which `summary()` still reads).
- **Collapse-by-default (2026-06-20):** a `collapseExamples()` pass in `render()` folds supplementary
  cards (Example / Worked example / Check-your-understanding / Formulas, incl. headerless formula
  boxes) into closed `<details>`; `U.faq`/`U.checklist` and the `U.mtable` tables are collapsible too.
  Worked-math tables default **open** (teaching-first): `U.setMath` opens them on first render and
  preserves the user's open/closed choice across keystroke re-renders.
- **Dev (localhost):** `python -m http.server 8000` -> http://localhost:8000 (or `npx http-server`).
  Preview config name `cpa-dashboard`, port 8740.
- **Build / test / lint / typecheck:** none configured. Verification = manual + the worked-example
  acceptance numbers (each page prefilled to a known answer; see HANDOFF.md). Also spot-check the
  worked-math tables (example vs. live) and that the collapse-by-default sections expand correctly.
- **Deploy:** Vercel static (`vercel.json` -> `cleanUrls`). **Git-connected, push-to-deploy VERIFIED**
  (2026-06-19): `git push origin main` -> Vercel auto-deploys to production. Manual fallback:
  `npx vercel --prod --scope jessica-dougherty-s-projects`. (`vercel git connect` SSH-alias gotcha
  documented in HANDOFF.md.)
- **Production:** https://cpa-dashboard-ashy.vercel.app + app.fastinsights.io/cpa-dashboard tile/proxy.
  Vercel project `cpa-dashboard` (team `jessica-dougherty-s-projects`). NOTE: plain
  `cpa-dashboard.vercel.app` is owned by someone else, so the prod alias is `cpa-dashboard-ashy`.

## GitHub / accounts (Jessica)
- This is **Jessica's** ecosystem (like the other Fast Insights tools). Vercel CLI on this machine
  is logged in as `jessicadougherty4321-6324` (team `jessica-dougherty-s-projects`).
- Repo: **`github.com/jessiesfaith/cpa-dashboard`** (public). Pushes use the SSH host alias
  **`github-jessica`** (key `~/.ssh/jessiesfaith_ed25519`); remote = `github-jessica:jessiesfaith/cpa-dashboard.git`.
  The machine's stored HTTPS credential is Chris's (`dogsleddev`) and CANNOT push to Jessica's repos.
- `gh` CLI is NOT logged in on this machine; the repo was created by Jessica on github.com, then
  `git push` + `vercel git connect` wired up push-to-deploy.

## Environment notes (this machine)
- **OneDrive Files-On-Demand:** this repo is inside a OneDrive-synced vault; files may be cloud
  placeholders that intermittently fail to read. Retry once; `git` reads fine.
