# EU Natural Gas Monitor

An interactive, single-file web dashboard for monitoring European natural gas storage, LNG terminal inventory, and spot prices across key markets. Built as part of a Master's Thesis research project.

**→ [View live dashboard](https://cetorre.github.io/eu-gas-monitor/)** 

---

## Overview

The dashboard provides a comprehensive view of the European natural gas market across four sections:

- **EU Storage Overview** — six key performance indicators updated on load: EU gas in storage (TWh), EU storage fill level (%), EU LNG inventory (GWh), EU LNG fill level (%), TTF front-month futures price (€/MWh), and Henry Hub futures price ($/MMBtu)
- **Country Storage (AGSI+)** — per-country underground gas storage for EU27, Germany, Netherlands, Austria, Italy, and Spain; absolute inventory (TWh), fill level (%), and a multi-country fill level comparison
- **LNG Terminal Inventory (ALSI+)** — LNG terminal data for EU, Germany, Netherlands, Italy, and Spain; absolute inventory (GWh), fill level (%), and a multi-country DTMI technical capacity comparison showing capacity additions over time
- **Natural Gas Spot Prices** — daily closing prices for TTF, PSV (Italy), THE (Germany), PVB (Spain), CEGH (Austria/CEE), and Henry Hub; includes a combined European hubs comparison chart and a price distribution box plot

All charts support time range selection: **3M · 6M · 1Y · 5Y · MAX**.

---

## Data Sources

| Data | Source | Coverage | Notes |
|------|--------|----------|-------|
| Underground gas storage | [GIE AGSI+](https://agsi.gie.eu) | 2020 – present | Requires free API key |
| LNG terminal inventory | [GIE ALSI+](https://alsi.gie.eu) | 2020 – present | Same API key as AGSI+ |
| TTF front-month futures | Yahoo Finance (`TTF=F`) | Live | Falls back to Bloomberg CSV if unavailable |
| Henry Hub futures | Yahoo Finance (`NG=F`) | Live | No historical fallback |
| TTF spot price (historical) | Bloomberg Terminal export | Jan 2020 – Jun 2024 | Embedded in file |
| PSV spot price (Italy) | Bloomberg Terminal export | Jan 2020 – Jun 2024 | Embedded in file |
| THE spot price (Germany) | Bloomberg Terminal export | Jan 2020 – Jun 2024 | Embedded in file |
| PVB spot price (Spain) | Bloomberg Terminal export | Jan 2020 – Jun 2024 | Embedded in file |
| CEGH spot price (Austria) | Bloomberg Terminal export | Jan 2020 – Jun 2024 | Embedded in file |

**Note on Bloomberg data:** The historical spot price series (TTF, PSV, THE, PVB, CEGH) are embedded directly in the HTML file as JavaScript arrays and load instantly with no network request. No Bloomberg subscription is required to view the data.

---

## Getting Started

### Option A — DEMO MODE (no setup required)

Open the dashboard and click **DEMO MODE**. This loads:
- Simulated seasonal storage and LNG data (realistic patterns, not live)
- Real Bloomberg CSV spot prices for all five European hubs (Jan 2020 – Jun 2024)
- Live TTF and Henry Hub prices via Yahoo Finance if available

This is the recommended mode for most users.

### Option B — Live AGSI/ALSI data

To load live underground storage and LNG data from the GIE APIs:

1. Register for a free API key at [agsi.gie.eu/account](https://agsi.gie.eu/account) — the same key works for both AGSI+ and ALSI+
2. Because the GIE API restricts direct browser requests (CORS policy), you need to serve the file from a local server rather than opening it directly:

```bash
# In the folder containing index.html
python3 -m http.server 8000
```

Then open [localhost:8000](http://localhost:8000) in your browser, enter your API key, and click **LOAD DATA**.

### Status indicators

The banner shows three coloured dots once data has loaded:

- 🟢 **AGSI/ALSI** — storage and LNG data loaded successfully
- 🟢 **TTF** — live Yahoo Finance feed active
- 🟢 **HH** — live Yahoo Finance feed active

Dots turn red if a feed is unavailable. The dashboard always falls back gracefully to embedded or simulated data.

---

## Technical Details

The dashboard is a **fully self-contained single HTML file** — no build step, no npm, no server required for the spot price data. Key implementation details:

- **No external charting library** — charts are rendered using a custom lightweight canvas engine (~400 lines) built into the file, replacing the Chart.js dependency to ensure the dashboard works in any browser environment including sandboxed previews
- **No external dependencies** — the only network requests at runtime are the GIE API calls (optional, require key) and Yahoo Finance fetches (optional, no key needed)
- **Embedded data** — all Bloomberg CSV historical spot prices are embedded as JavaScript arrays, totalling approximately 6,000 data points across five hubs
- **Responsive layout** — adapts to desktop and tablet screen widths

### Hub colour scheme

The colour palette follows the thesis visual identity:

| Hub / Country | Colour |
|---------------|--------|
| TTF / Netherlands | `#00b4d8` |
| Germany (THE/DE) | `#136f63` |
| Spain (PVB/ES) | `#76c893` |
| Italy (PSV/IT) | `#90e0ef` |
| Austria (CEGH/AT) | `#eab308` |
| Henry Hub | `#ec9a9a` |
| EU aggregate | `#00d4aa` |

---

## Repository Structure

```
/
├── index.html      # The complete dashboard (single file)
└── README.md       # This file
```

---

## Known Limitations

- **AGSI/ALSI CORS restriction** — the GIE API is designed for server-side use and blocks direct browser requests. Live storage data requires a local server (see Getting Started above).
- **Yahoo Finance availability** — Yahoo Finance has intermittently restricted browser-side API access since mid-2024 due to cookie/crumb authentication requirements. If the TTF and HH dots show red, this is a known upstream limitation and does not affect the Bloomberg CSV historical data or AGSI storage data.
- **Spot price coverage** — Bloomberg CSV data covers January 2020 to June 2024. The 1M and 6M time range buttons will show "no data in this range" for spot price charts since the data predates those windows.

---

## Academic Context

This dashboard was developed as part of a Master's Thesis research project analysing European natural gas market dynamics, with a particular focus on the 2021–2023 energy crisis, LNG infrastructure expansion, and price transmission across European hubs.
https://cetorre.github.io/thesis-onepager/
---

## License

This project is for academic research purposes. The Bloomberg Terminal export data is proprietary and included solely for research and educational use within the scope of the associated thesis.

GIE AGSI+ and ALSI+ data is sourced under the terms of the [GIE Terms of Use](https://agsi.gie.eu).
