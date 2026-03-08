# COMP 6940 Assignment 1 — Part 2: Air Quality + Weather Analysis

**Cities:** New York City · Los Angeles · Chicago
**Period:** 2025-01-01 to 2025-03-31 (Q1)
**Pollutant:** PM2.5

---

## Submission Checklist

### Repository Structure

| Item | Status | Notes |
|------|--------|-------|
| `README.md` | ✅ Done | This file |
| `requirements.txt` / `environment.yml` | ❌ **Missing** | Must be created before submission |
| `data/raw/openaq/pm25_nyc_2025Q1.json` | ✅ Done | 90 daily records |
| `data/raw/openaq/pm25_la_2025Q1.json` | ✅ Done | 90 daily records |
| `data/raw/openaq/pm25_chicago_2025Q1.json` | ✅ Done | 90 daily records |
| `data/raw/openmeteo/weather_nyc_2025Q1.json` | ✅ Done | 2160 hourly records |
| `data/raw/openmeteo/weather_la_2025Q1.json` | ✅ Done | 2160 hourly records |
| `data/raw/openmeteo/weather_chicago_2025Q1.json` | ✅ Done | 2160 hourly records |
| `data/curated/part2_airquality_curated.parquet` | ✅ Done | 270 rows × 9 columns |
| `part2_airquality/01_ingest.ipynb` | ✅ Done | See §01 below |
| `part2_airquality/02_clean_features.ipynb` | ✅ Done | See §02 below |
| `part2_airquality/03_stats_eda_viz.ipynb` | ✅ Done | See §03 below |
| `part2_airquality/report.pdf` | ⚠️ **Incomplete** | `report.docx` exists — must be converted to PDF |
| `part2_airquality/data_dictionary.pdf` | ❌ **Missing** | Must be created before submission |

---

### 01\_ingest.ipynb

| Requirement | Status | Notes |
|-------------|--------|-------|
| Retrieve PM2.5 from OpenAQ v3 for NYC, LA, Chicago | ✅ Done | Sensor IDs: NYC 3140, LA 4913, Chicago 12863 |
| Retrieve hourly weather from Open-Meteo for all three cities | ✅ Done | Vars: temp, wind speed, precipitation |
| Date range: 2025-01-01 → 2025-03-31 | ✅ Done | |
| Save raw AQ to `data/raw/openaq/` (correct filenames) | ✅ Done | |
| Save raw weather to `data/raw/openmeteo/` (correct filenames) | ✅ Done | |
| Print row counts for air quality per city | ✅ Done | 90 daily records each |
| Print row counts for weather per city | ✅ Done | 2160 hourly records each |

---

### 02\_clean\_features.ipynb

| Requirement | Status | Notes |
|-------------|--------|-------|
| Output saved to `data/curated/part2_airquality_curated.parquet` | ✅ Done | |
| One row per (city, date) | ✅ Done | 270 rows total |
| **Air quality fields:** `city`, `date`, `pm25_mean`, `pm25_p90` | ✅ Done | |
| **Weather fields:** `temp_mean`, `wind_speed_mean`, `precip_sum` | ✅ Done | |
| **Calendar fields:** `weekday`, `is_weekend` | ✅ Done | |
| QA Rule 1: Remove exact-duplicate PM2.5 records | ✅ Done | 0 duplicates found |
| QA Rule 2: Convert timestamps to consistent timezone (UTC) | ✅ Done | UTC stated and justified |
| QA Rule 3: Days with < 12 observations → pm25_mean/pm25_p90 = NaN | ✅ Done | 0 days flagged |
| QA Rule 4: Drop missing PM2.5 days; report count per city | ✅ Done | 0 days dropped for all cities |
| No PM2.5 interpolation | ✅ Done | |

---

### 03\_stats\_eda\_viz.ipynb

#### A. Group-by Metrics (Pandas + DuckDB)

| Requirement | Status | Notes |
|-------------|--------|-------|
| A1. mean, median, std of `pm25_mean` per city | ✅ Done | DuckDB + Pandas cross-check |
| A2. mean, median, std of `pm25_p90` per city | ✅ Done | DuckDB + Pandas cross-check |
| A3. mean `pm25_mean` by (city, is\_weekend) | ✅ Done | Both Pandas and DuckDB |
| A4. mean `pm25_mean` by (city, weekday) | ✅ Done | Both Pandas and DuckDB |

#### B. Window-Style Metrics (Pandas)

| Requirement | Status | Notes |
|-------------|--------|-------|
| B1. Rolling 7-day mean of `pm25_mean` per city ordered by date | ✅ Done | `groupby + rolling(7, min_periods=1)` |
| B2. Day-over-day difference of `pm25_mean` per city | ✅ Done | `groupby + diff()` |
| B2. Day-over-day percent change of `pm25_mean` per city | ✅ Done | `groupby + pct_change() × 100` |

#### C. EDA Questions

| Requirement | Status | Notes |
|-------------|--------|-------|
| C1. Pearson correlation: pm25_mean vs temp, wind, precip per city | ✅ Done | DuckDB `CORR()` |
| C1. State which correlation is strongest | ✅ Done | NYC: wind (r = −0.638); CHI: wind (r = −0.237); LA: temp (r = +0.175) |
| C1. Discuss one confounder | ✅ Done | Seasonal inversion layers discussed |
| C2. Define "high PM2.5" as top 5% per city | ✅ Done | Thresholds: CHI 13.18, LA 28.17, NYC 14.87 µg/m³ |
| C2. Identify two episodes per city | ✅ Done | 6 episodes total with dates |
| C2. Describe corresponding weather values | ✅ Done | temp, wind, precip for each episode |
| C3. Determine most volatile city | ✅ Done | LA (std = 12.03 µg/m³) |
| C3. Report std of `pm25_mean` for all three cities | ✅ Done | LA 12.03, CHI 3.99, NYC 3.91 |

#### D. Visualizations (all in `part2_airquality/figures/`)

| Requirement | Status | Notes |
|-------------|--------|-------|
| D1. Time series of pm25_mean for all 3 cities + rolling 7-day means overlaid | ✅ Done | `fig1_timeseries_rolling7.png` |
| D2. Scatter plot: pm25_mean vs wind\_speed\_mean, separated by city | ✅ Done | `fig2_scatter_pm25_wind.png` |
| D3. Distribution comparison of pm25_mean across cities | ✅ Done | `fig3_distribution_comparison.png` (box + violin) |
| D4. Heatmap comparing cities over time (pm25_mean intensity) | ✅ Done | `fig4_heatmap_city_time.png` |
| All plots have titles | ✅ Done | |
| All plots have labeled axes with units | ✅ Done | |

---

### Report

| Requirement | Status | Notes |
|-------------|--------|-------|
| Dataset description (grain, dates, variables) | ✅ Done | In `report.docx` §1 |
| QA rules and missing-day count per city | ✅ Done | In `report.docx` §2 |
| Required metrics with interpretation | ✅ Done | In `report.docx` §3–4 |
| Required four plots with captions | ✅ Done | In `report.docx` §5 |
| Limitations and additional data desired | ✅ Done | In `report.docx` §6 |
| **Format: PDF** (`report.pdf`) | ⚠️ **Incomplete** | Currently `report.docx` — convert to PDF |

---

### Data Dictionary

| Requirement | Status | Notes |
|-------------|--------|-------|
| `data_dictionary.pdf` describing curated dataset | ❌ **Missing** | Column name, type, unit, meaning, notes for all 9 columns |

---

### Hard Requirements

| Requirement | Status | Notes |
|-------------|--------|-------|
| Group-by metrics computed in Pandas or DuckDB | ✅ Done | Both used with cross-checks |
| Window metrics computed in Pandas or DuckDB | ✅ Done | Pandas `rolling`, `diff`, `pct_change` |
| Each plot has title + labeled axes + units | ✅ Done | |
| All work in `.ipynb` notebooks | ✅ Done | |
| Runs end-to-end following README | ⚠️ **Partial** | Needs `requirements.txt`; API calls skip re-fetching if files exist |
| Raw data saved under `data/raw/` | ✅ Done | |
| Curated data saved under `data/curated/` | ✅ Done | |
| `data_dictionary.pdf` | ❌ **Missing** | |
| `requirements.txt` or `environment.yml` | ❌ **Missing** | |

---

## What Still Needs to Be Done

### ❌ Must-Do Before Submission

1. **Convert `report.docx` → `report.pdf`**
   Open `part2_airquality/report.docx` in Microsoft Word (or LibreOffice) and export/save as PDF named `report.pdf` in the same folder.

2. **Create `data_dictionary.pdf`**
   Must document all 9 columns of `data/curated/part2_airquality_curated.parquet`:

   | Column | Type | Unit | Meaning | Notes |
   |--------|------|------|---------|-------|
   | city | String | — | City name | One of: Chicago, Los Angeles, New York City |
   | date | Timestamp (UTC) | — | Calendar date at UTC midnight | Timezone-aware; UTC used for consistency across city offsets |
   | pm25_mean | Float64 | µg/m³ | Daily mean PM2.5 concentration | From OpenAQ v3 daily sensor aggregate (`summary.avg`) |
   | pm25_p90 | Float64 | µg/m³ | Interpolated daily 90th-percentile PM2.5 | Linearly interpolated from Q75 and Q98 (OpenAQ does not expose Q90 directly) |
   | temp_mean | Float64 | °C | Daily mean air temperature at 2 m height | Mean of hourly Open-Meteo `temperature_2m` values |
   | wind_speed_mean | Float64 | km/h | Daily mean wind speed at 10 m height | Mean of hourly Open-Meteo `windspeed_10m` values |
   | precip_sum | Float64 | mm | Daily total precipitation | Sum of hourly Open-Meteo `precipitation` values |
   | weekday | String | — | Day of the week | One of: Monday, Tuesday, …, Sunday |
   | is_weekend | Int64 | 0 / 1 | Weekend flag | 1 = Saturday or Sunday; 0 = weekday |

   Create this as a PDF (Word, Google Docs, or LaTeX).

3. **Create `requirements.txt` or `environment.yml`**
   Minimum packages needed to reproduce all notebooks:
   ```
   pandas
   numpy
   requests
   duckdb
   matplotlib
   seaborn
   pyarrow
   python-docx
   jupyter
   ```

4. **Submission email**
   Send to `inzamam.rahaman@uwi.edu` (CC: `kris.manohar@sta.uwi.edu`)
   Subject: `COMP6940 A1 SUBMISSION`
   Body must include:
   - All group members: **Name** and **Student ID**
   - Chosen option: **Part 2 — Air Quality + Weather**

---

## How to Run (End-to-End)

### Prerequisites

```bash
pip install -r requirements.txt
```

### Run Order

```bash
# 1. Data ingestion (fetches from OpenAQ and Open-Meteo APIs)
jupyter nbconvert --to notebook --execute part2_airquality/01_ingest.ipynb --inplace

# 2. Cleaning, QA, and feature engineering
jupyter nbconvert --to notebook --execute part2_airquality/02_clean_features.ipynb --inplace

# 3. Statistics, EDA, and visualizations
jupyter nbconvert --to notebook --execute part2_airquality/03_stats_eda_viz.ipynb --inplace
```

> **Note:** Notebooks 01 and 02 skip API calls if raw files already exist in `data/raw/`.
> Delete `data/raw/` contents to force a fresh fetch.

---

## Repository Structure

```
project/
├── README.md
├── requirements.txt          ← MISSING — must create
├── data/
│   ├── raw/
│   │   ├── openaq/
│   │   │   ├── pm25_nyc_2025Q1.json
│   │   │   ├── pm25_la_2025Q1.json
│   │   │   └── pm25_chicago_2025Q1.json
│   │   └── openmeteo/
│   │       ├── weather_nyc_2025Q1.json
│   │       ├── weather_la_2025Q1.json
│   │       └── weather_chicago_2025Q1.json
│   └── curated/
│       └── part2_airquality_curated.parquet
└── part2_airquality/
    ├── 01_ingest.ipynb
    ├── 02_clean_features.ipynb
    ├── 03_stats_eda_viz.ipynb
    ├── figures/
    │   ├── fig1_timeseries_rolling7.png
    │   ├── fig2_scatter_pm25_wind.png
    │   ├── fig3_distribution_comparison.png
    │   └── fig4_heatmap_city_time.png
    ├── report.docx            ← convert to report.pdf
    ├── report.pdf             ← MISSING — convert from report.docx
    └── data_dictionary.pdf   ← MISSING — must create
```

---

## Data Sources

| Source | API | What Was Retrieved |
|--------|-----|--------------------|
| [OpenAQ v3](https://docs.openaq.org/) | `/v3/sensors/{id}/measurements?period_name=day` | Daily PM2.5 aggregates (mean, Q75, Q98, observation count) |
| [Open-Meteo Historical](https://open-meteo.com/en/docs/historical-weather-api) | `/v1/archive` | Hourly temperature, wind speed, precipitation |

---

## Key Results Summary

| City | Mean PM2.5 | Std Dev | Peak Day | Days > WHO (15 µg/m³) |
|------|-----------|---------|----------|----------------------|
| Los Angeles | 13.17 µg/m³ | 12.03 | 91.26 (Jan 8, wildfire) | 22 |
| Chicago | 7.21 µg/m³ | 3.99 | 27.51 (Mar 15, agricultural burn) | 3 |
| New York City | 7.12 µg/m³ | 3.91 | 16.73 (Mar 31, stagnation) | 4 |

**Most volatile city:** Los Angeles (std = 12.03 µg/m³, driven by January 2025 wildfire events)
**Strongest weather predictor of PM2.5:** Wind speed in NYC (Pearson r = −0.638)
