# Temperature vs Demand

A single-page search tool combining historical temperature observations with electricity grid demand data for Australia and Japan.

**Live tool:** [2manytims.github.io/Temp_Vs_Demand](https://2manytims.github.io/Temp_Vs_Demand)

---

## What it does

Answers questions like:

- *Last time Adelaide had a maximum ≥ 40°C — what was SA grid demand?*
- *How many times has Tokyo had 5+ consecutive days below 0°C overnight?*
- *What were conditions in Melbourne on 13 January 2009?*

Temperature and demand data are matched by date, with demand shown as a raw MW figure and a percentile badge (Top 1% / Top 5% / Top 10% / Top 25% / Above median / Below median) calibrated against the full historical record.

---

## Coverage

| | Locations | Temperature record | Demand record |
|---|---|---|---|
| **Australia** | Adelaide, Melbourne, Archerfield, Bankstown | ~30 years (BOM) | 2010 to present (AEMO) |
| **Japan** | Tokyo, Osaka, Nagoya, Fukuoka, Sapporo, Sendai, Hiroshima, Matsuyama, Kanazawa | ~30 years (JMA) | 2016 to present |

### Australian station notes
Adelaide observations splice two stations: Kent Town (pre-2017) and West Terrace (2017 onward). Melbourne splices Regional Office (pre-2013) and Olympic Park (2013 onward). Old-station rows are flagged in search results.

---

## Search modes

### Threshold Search
Filter all records by location, temperature type (max/min), condition (≥ / ≤ / ≈), threshold, and optional month filter. Returns a ranked table with demand context for each matching day, plus summary statistics (total days, most recent, hottest/coldest, mean, most active year).

### Date Lookup
Enter a specific date and location to see the daily maximum, daily minimum, demand for that day, and percentile context for each.

### Streak Search
Find all runs of consecutive days meeting a temperature threshold. Set a minimum streak length to filter out short events. Results show start date, end date, streak length, hottest/coldest day in the streak, mean temperature, and the peak demand day within the streak.

---

## Data sources

- **Australian temperatures:** [Bureau of Meteorology](https://www.bom.gov.au) — daily max/min observations
- **Australian demand:** [AEMO](https://www.aemo.com.au) — aggregated price and demand CSVs, TRADE period type, daily peak
- **Japan temperatures:** [Japan Meteorological Agency](https://www.jma.go.jp) — daily max/min observations
- **Japan demand:** [japanesepower.org](https://japanesepower.org) — half-hourly regional CSVs, daily peak

---

## Repository structure

```
Temp_Vs_Demand/
  index.html                  — the search tool
  aus_observations.json       — AU daily Tmax/Tmin, 4 stations
  aus_demand.json             — AU daily peak demand, 4 regions (SA, VIC, NSW, QLD)
  japan_observations.json     — JP daily Tmax/Tmin, 9 stations
  japan_demand.json           — JP daily peak demand, 9 regions
```

The JSON files are the only data layer. The tool fetches them on load and runs entirely in the browser — no backend, no build step.

---

## Monthly update

Data is refreshed once a month using `monthly_update.py`, which lives outside this repo on a local machine alongside the source CSV files.

The script:
1. Downloads latest BOM observations for all 4 Australian stations
2. Fetches latest Japan demand from japanesepower.org
3. Loads `aus_demand_historical.csv` and appends only the current month from AEMO (avoids re-fetching the full history each run)
4. Rebuilds all four JSON files
5. Assembles a `GitHub_Upload_[Month]_[Year]/` folder ready for upload

To update the live tool, upload the contents of that folder to this repo and commit.

---

## Local historical data

Australian demand history is stored locally as `aus_demand_historical.csv` (date, SA1, VIC1, NSW1, QLD1 daily peaks from January 2010). This file is not in the repo — it lives on the update machine and grows by one month each run.
