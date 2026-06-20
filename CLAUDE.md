# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page **Trade Setup Calculator** for leveraged (long/short) trading. Given position type, leverage, margin, entry price, and fee, it computes a fixed ladder of profit-target prices (5–200%), an optional custom target (in % or USD), and the estimated liquidation price using a tiered maintenance-margin rate. It is a static client-side app — no backend, no build step, no dependencies installed locally.

## Running / developing

- The app is **plain HTML + inline JavaScript**, styled with Tailwind loaded from a CDN (`https://cdn.tailwindcss.com`). Open the file directly in a browser to run it; there is no server, build, lint, or test tooling.
- Because Tailwind comes from a CDN, **styling requires network access** when the page loads.

## Important file gotcha

- **`calculator`** (no extension) is the real, working application — open this in a browser.
- **`index.html`** is NOT the app. It is a TextEdit/Cocoa "Save as HTML" export that renders the calculator's source as escaped, human-readable text (note the `Cocoa HTML Writer` generator meta and `<p class="p1">&lt;...&gt;</p>` markup). Editing it does not change app behavior.
- The two files are meant to be the same program but can drift. **Make functional changes in `calculator`.** If `index.html` must stay in sync, regenerate/update it as the escaped-source mirror — do not treat it as runnable.

## Calculation model (in `calculator`)

All logic lives in the inline `<script>`:

- **Profit targets** iterate `targetPercents = [5,10,15,25,50,75,100,150,200]`. Each percent is treated as a percentage *of margin* (P&L target), converted to a price move via `priceMove = targetUSD / (margin * leverage)`, then applied as `entry ± entry*priceMove` (`+` for long, `−` for short).
- **Custom target** uses the same formula; `customType` switches between interpreting the input as a percent of margin or a raw USD amount.
- **Liquidation price** derives `positionSize = (margin * leverage) / entry`, looks up a tiered maintenance-margin rate via `getMaintenanceMarginRate(positionSizeUSD)` (0.5% ≤50k USD up to 2.5% >1M USD), and solves for the liquidation price separately for long vs short.
- The `fee` input is currently read but not used in any calculation — relevant if fee-adjusted results are requested.
