# EV Infrastructure Gap Analysis
**Tools:** R · RMarkdown · tidyverse · ggplot2 · sf · tidycensus · glm (Poisson)  
**Data:** US Department of Energy · US Census Bureau ACS (2022) · 50-State Coverage

---

## Overview

This project quantifies where EV charging infrastructure is falling behind market demand across all 50 US states. Using publicly available data from the US Department of Energy and the Census Bureau, the analysis builds a state-level dataset, models the expected charging station footprint using a Poisson regression framework, and produces spatial coverage maps that identify which populations are currently outside practical EV range.

The core question driving the work: **given EV adoption trends, state population, geography, and the precedent set by mature gasoline infrastructure, how many EV stations *should* each state have — and how far is each state from that target?**

---

## Analytical Workflow

| Phase | Notebook | Description |
|---|---|---|
| 1. Preprocessing | `ev_analysis_preprocessing.Rmd` | Ingests 5 source datasets, applies `janitor` for consistent column naming, filters to the US market, and engineers derived metrics: EVs per station, people per station, and EV saturation rate (EV registrations ÷ population) |
| 2. EDA | `ev_analysis_eda.Rmd` | Produces ranked bar charts, a point-density map of all continental US station locations, and three choropleth maps visualizing demand pressure across states |
| 3. Modeling | `ev_analysis_modeling.Rmd` | Trains a Poisson GLM to predict expected station counts per state; calculates a **station gap** (expected − actual) to rank underbuilt markets |
| 4. Geospatial | `ev_analysis_geospatial.Rmd` | Generates county-level spatial coverage maps at 1-, 5-, and 10-mile radii for the top 5 underbuilt states, overlaid on population density; Pennsylvania is included as a supplementary market |

---

## Data Sources

| Dataset | Source |
|---|---|
| EV Charging Station Locations | [AFDC Alternative Fueling Station Locator](https://afdc.energy.gov/data_download) — July 2025 export |
| EV Registrations by State | [AFDC Table 10962](https://afdc.energy.gov/data/10962) |
| State Population Estimates | [US Census Bureau — 2020s State Totals](https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html) |
| Gasoline Stations by State | [AFDC Historical Gas Station Data (2012)](https://afdc.energy.gov/files/u/data/data_source/10333/10333_gasoline_stations_year.xlsx) |
| State Geographic Area | [US Census Bureau — State Area Reference](https://www.census.gov/geographies/reference-files/2010/geo/state-area.html) |
| County Population & Geometry | US Census Bureau ACS via `tidycensus` (2022, `B01003_001`) |

---

## Methodology

### Benchmark: Gasoline Station Infrastructure

A key modeling assumption is that the average ICE driver does not experience meaningful range anxiety — gasoline stations are sufficiently dense, distributed, and reliable that fueling is frictionless. This project uses state-level **2012 gasoline station counts** as a maturity benchmark: a proxy for what a fully developed fueling network looks like in each state, adjusted for geographic size and population.

This framing anchors the modeling question not just in current EV demand, but in what a *parity* infrastructure footprint would require.

### Poisson Regression Model

A Poisson GLM with log link was selected because the response variable (total EV stations per state) represents count data with a non-negative, right-skewed distribution — the standard assumption set for count regression.

**Predictors:**
- `ev_registrations` — current registered EV count by state
- `population` — total state population (Census estimate)
- `gas_stations` — 2012 gasoline station count as a density/maturity benchmark
- `sq_mi` — total state area in square miles

**Derived outputs:**
- `expected_stations` — model-predicted EV station count per state
- `station_gap` — expected stations minus actual stations; positive values indicate underbuilt markets

### Spatial Coverage Analysis

The geospatial phase uses `sf` with a CONUS Albers Equal Area Conic projection (EPSG:5070) to accurately represent station reach. For each target state, the analysis:

1. Projects individual station coordinates and generates **circular buffers at 1, 5, and 10 miles**
2. Dissolves overlapping buffers into a unified coverage area
3. Subtracts covered area from the full state geometry to isolate uncovered regions
4. Overlays results on county-level population density (log-scaled) to identify **which populations** fall outside current coverage — not just which land area

Coverage maps are generated for the top 5 underbuilt states (as ranked by station gap) plus Pennsylvania.

---

## Key Findings

1. **California** leads all states in total EV station count and EV adoption rate (registrations ÷ population) by a wide margin. Coastal metros on both coasts represent the most developed markets.
2. **Coastal regions broadly** outperform the interior US in station density; the Midwest and Deep South are measurably underdeveloped relative to population.
3. **New Jersey** has the highest EV-to-station ratio in the country, indicating that its existing infrastructure is already under pressure relative to registered vehicles.
4. **Top 5 underbuilt states by model:** Florida, Louisiana, West Virginia, Kentucky, and Mississippi. These states show the largest positive station gaps — where expected infrastructure exceeds current buildout by the widest margin.
5. **Pennsylvania:** Eastern (Philadelphia metro) and western (Pittsburgh metro) regions show relatively dense station coverage. Central and northern Pennsylvania — including large rural and exurban areas — fall outside practical coverage at all three buffer radii analyzed.

---

## Repository Structure

```
ev_infrastructure_analysis/
│
├── data/                          # Source and intermediate datasets
│   ├── alt_fuel_stations (Jul 7 2025).csv
│   ├── ev_registration_by_state.csv
│   ├── state_populations.csv
│   ├── 2012_gas_stations_us.csv
│   ├── state_areas.csv
│   ├── ev_data_by_state.csv       # Preprocessed output
│   ├── ev_station_locations.csv   # Continental US stations (geo export)
│   └── underbuilt_states.rds      # Top 5 states list for geospatial phase
│
├── outputs/
│   ├── charts/                    # Bar charts (top 10 states, station gap)
│   ├── usa_maps/                  # National choropleth maps
│   ├── modeling/                  # Station gap map and top underbuilt states
│   └── coverage_maps/             # Per-state spatial coverage maps (1/5/10 mi)
│       ├── FL/
│       ├── LA/
│       ├── WV/
│       ├── KY/
│       ├── MS/
│       └── PA/
│
├── ev_analysis_preprocessing.Rmd
├── ev_analysis_eda.Rmd
├── ev_analysis_modeling.Rmd
├── ev_analysis_geospatial.Rmd
│
├── ev_analysis_preprocessing_nb.html
├── ev_analysis_eda_nb.html
├── ev_analysis_modeling_nb.html
└── ev_analysis_geospatial_nb.html
```

---

## Technical Stack

| Category | Tools |
|---|---|
| Language & Environment | R, RStudio, RMarkdown |
| Data Wrangling | `tidyverse`, `dplyr`, `janitor`, `Hmisc` |
| Visualization | `ggplot2`, `usmap`, `maps`, `scales` |
| Geospatial | `sf`, `tidycensus` |
| Modeling | Base R `glm` (Poisson family, log link) |

---

## Potential Extensions

- **API automation:** Replace the static DoE export with scheduled pulls from the [AFDC Station Locator API](https://developer.nrel.gov/docs/transportation/alt-fuel-stations-v1/) to enable quarterly station gap tracking as infrastructure evolves.
- **Routing and proximity features:** Incorporate highway corridor proximity and urban/rural classification as additional predictors to refine site-level prioritization within underbuilt states.
- **International benchmarking:** Compare US state-level metrics against Norway — the global leader in per-capita EV adoption — to contextualize the scale of infrastructure investment required.
- **Demand forecasting:** Extend the model to project forward station needs under different EV adoption growth scenarios (e.g., 10%, 25%, 50% penetration) by state.