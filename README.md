# Big Tech Forecasting Dashboard

A single-file, zero-dependency web dashboard that runs **live revenue forecasting**
for 10 of the world's largest technology companies — right in the browser, with no
server, build step, or internet connection required.

Open `index.html` and you get a home screen of company cards; click any one to drill
into its full forecasting dashboard.

![Companies covered: NVIDIA, Apple, Microsoft, Alphabet, Amazon, Meta, Broadcom, Tesla, TSMC, Oracle](https://img.shields.io/badge/companies-10-4a3aa7) ![No dependencies](https://img.shields.io/badge/dependencies-0-0e8c60) ![Single file](https://img.shields.io/badge/build-none-eb6834)

## Companies

NVIDIA · Apple · Microsoft · Alphabet · Amazon · Meta Platforms · Broadcom · Tesla ·
TSMC · Oracle — each with ~12 quarters of segment-level revenue and GAAP gross margin
compiled from company reports.

## What it does

- **Three forecasting models**, switchable per company:
  - **Holt-Winters** triple exponential smoothing (level + trend + quarterly seasonality)
  - **Exponential trend** (log-linear regression with a seasonal component)
  - **Seasonal moving average**
- Revenue is forecast in **log-space** (tech revenue compounds), with an **80%
  confidence band** that widens with horizon.
- **Backtesting**: each model is retrained without the last 4 quarters and scored by
  **MAPE** against what the company actually reported, so you can see which model fits.
- **Segment forecasts** are reconciled to the headline revenue forecast, so the KPI
  tile, donut, and stacked bars always agree.
- Adjustable **horizon** (2 / 4 / 6 quarters) and **history window**.

## Dashboard panels

- KPI tiles — trailing revenue, forecast, backtest accuracy, gross margin (with sparklines)
- Quarterly revenue forecast with confidence band and hover crosshair
- Actual-vs-forecast backtest bars + per-model MAPE
- Forecast revenue mix by segment (donut)
- Quarterly revenue by segment (stacked bars, history + forecast)
- Gross-margin trend and forecast

## Design

- UI/UX modeled on a clean purple SaaS dashboard (sidebar nav, rounded cards, soft shadows).
- Charts follow a colorblind-validated categorical palette; every chart has a legend,
  tooltips, and a "View data" table.

## Automated monthly refresh

A companion Claude Code scheduled task (`nvidia-forecast-data-refresh`) runs on the 1st
of each month: it checks each company for newly reported quarters, pulls the figures from
primary sources (press releases / SEC filings), replaces any estimated values with
reported ones, and edits the `COMPANIES` data object in `index.html` in place. Because
every chart recomputes from that object, the forecasts update automatically.

## Updating the data by hand

All data lives in the `COMPANIES` object near the top of the `<script>` block in
`index.html`. Each company has a `segs` list and `rows` of
`{ label, vals:[…], margin, est? }` (revenue in USD billions). Add a quarter, and every
chart, KPI, and forecast recomputes on the next page load.

## Disclaimer

Figures are compiled from public company reports; some of the most recent quarters are
estimates (flagged `est: true` / `~`). Forecasts are illustrative and **not investment
advice**.
