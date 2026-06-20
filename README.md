# CPA Corporate Tax Study & Calculator

A single-page, CPA-style study guide **and** planning calculator for four corporate-tax topics.
Vanilla HTML/CSS/JS — no build step, no dependencies — in the Fast Insights house style.

## Sections (hash-routed)

| Top tab | Sub-tab | Route |
|---|---|---|
| Corporation | NOL Section 382 | `#/corporation/nol-section-382` |
| Corporation | Consolidations | `#/corporation/consolidations` |
| Property | Property for Stock – SH | `#/property/property-for-stock-sh` |
| Dividend | Accumulated and Current E&P | `#/dividend/accumulated-current-ep` |
| Liquidation | Stock-Gain/Loss | `#/liquidation/stock-gain-loss` |

Each page has: an education disclaimer, an overview, formula boxes, step-by-step worked
examples, answer-choice cards, large currency results, input validation, a collapsible FAQ,
an advisor-review checklist, and copy/export + print.

> **Disclaimer:** for education and planning support only. Tax rules are technical and should be
> reviewed by a qualified tax advisor before relying on any result. Simplified CPA study mode —
> not a tax return.

## Local dev

```
python -m http.server 8000      # then open http://localhost:8000
# or
npx http-server -p 8000 -c-1
```

## Architecture

`index.html` is the whole app: a small framework (hash router, top/sub-tab nav, shared `U`
helper API, auto-rendered disclaimer + Copy-summary/Print buttons) plus five **page modules**.
Each page registers itself with one call:

```js
registerPage({ top, topLabel, sub, subLabel, title, html, init(root), summary(root) });
```

To add a page, append another `registerPage({...})` `<script>` block. Shared helpers
(`U.fmtUSD`, `U.normRate`, `U.choices`, `U.faq`, `U.checklist`, `U.copy`, …) and CSS classes
are documented inline at the top of `index.html`.

## Deploy

Static site on Vercel (`cleanUrls: true`), git-connected: **`git push origin main` auto-deploys**
(repo `github.com/jessiesfaith/cpa-dashboard`). Manual fallback:
`npx vercel --prod --scope jessica-dougherty-s-projects`. Production:
https://cpa-dashboard-ashy.vercel.app · also surfaced at app.fastinsights.io/cpa-dashboard.
