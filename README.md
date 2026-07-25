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

## Route Planner (Routing tab)

An event-aware, cost-optimal freight routing planner — the third sidebar tab. It treats
real-world disruptions (e.g. a political rally / road closure) as a congestion cost and
reroutes to minimise total delivered cost. Four sub-tabs:

- **Event scenario** — a worked Chandigarh → Agra example over a modelled regional road
  graph. A real event (Delhi Monsoon Session of Parliament road restrictions, Jul 2026)
  is priced as a congestion penalty; the planner picks the cheapest corridor by exhaustive
  path search. Toggle the event and drag a delay-severity slider to watch the reroute
  decision flip at its cost break-even point.
- **Plan a route** — enter any origin/destination. Geocoded via **OpenStreetMap Nominatim**
  and routed on **real roads via OSRM** (both keyless) when online, with an offline
  haversine estimate as fallback. Checks each road alternative against the events layer and
  recommends the cheapest, auto-detouring around disruptions.
- **Fleet / batch upload** — upload a CSV of shipments (`origin,destination[,vehicle]`) for
  batch pricing and event-risk flagging, or run the **multi-stop optimiser (VRP)** —
  nearest-neighbour + 2-opt — to order a set of stops into the shortest round trip.
- **Cost & events** — a configurable cost model (diesel price, driver/idle rates, vehicle
  class → derived ₹/km) plus a **pluggable events layer** seeded from real traffic
  advisories, and a localStorage **learning log** of planned routes.

Cost model, distances, and tolls are illustrative; live traffic-incident and toll feeds are
left as pluggable stubs because they require API keys / a backend. See "Making it better"
in the project notes for the production roadmap (real directions API, live event feeds,
VRP with constraints, learning loop).

## Design

- UI/UX modeled on a clean purple SaaS dashboard (sidebar nav, rounded cards, soft shadows).
- Charts follow a colorblind-validated categorical palette; every chart has a legend,
  tooltips, and a "View data" table.
- **Dark mode** (toggle in the top bar, remembers your choice and respects the system
  setting) — charts re-theme, not just invert.
- **Responsive** down to mobile; **deep-linkable** (the URL hash remembers the view, e.g.
  `#co/aapl` or `#route/plan`); **exportable** (forecast data to CSV, the chart to PNG).
- Accessibility: focus-visible outlines, ARIA labels, keyboard-navigable controls.

## Data trust

- Latest reported quarters are verified against primary sources; each company carries a
  **source link** and an **"as of" date** shown in the footer, and quarters that are still
  partly estimated are flagged with `~`.
- Totals and major segments are as-reported; some segment splits and the newest-quarter
  margins are approximate (stated in the footer). The monthly refresh task keeps them current.

## Forecasting quality

- Four models — Holt-Winters, exponential trend, seasonal moving average, and an
  **Ensemble** (their blend) — plus **Auto**, which picks the lowest-MAPE model per company
  (the default).
- Each forecast reports an **80% confidence-band calibration**: the share of held-out
  quarters the band actually covered, so you can see when a band is too tight or too wide.

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
