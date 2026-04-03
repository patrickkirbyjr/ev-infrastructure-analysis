# EV Infrastructure Analysis

## Summary
This project analyzes the distribution of EV charging stations across the United States to identify where infrastructure is most needed. Data on EV stations, registrations, state populations, gas stations, and geographic area were sourced from the US Department of Energy and US Census Bureau. A linear regression model was used to predict a **station gap** (expected stations minus current stations) for each state, and spatial coverage analysis was applied to the most underbuilt states.

## Project Structure

| Notebook | Description |
|---|---|
| `ev_analysis_preprocessing.Rmd` | Data ingestion, cleaning, joining, and derived metric calculation |
| `ev_analysis_eda.Rmd` | Exploratory visualizations including bar charts and choropleth maps |
| `ev_analysis_modeling.Rmd` | Linear regression modeling and station gap prediction |
| `ev_analysis_geospatial.Rmd` | Spatial coverage analysis for underbuilt states |

## Data Sources

| Dataset | Source |
|---|---|
| EV Station Data | https://afdc.energy.gov/data_download |
| EV Registration Data | https://afdc.energy.gov/data/10962 |
| State Population Data | https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html |
| Gas Station Data | https://afdc.energy.gov/files/u/data/data_source/10333/10333_gasoline_stations_year.xlsx |
| State Area Data | https://www.census.gov/geographies/reference-files/2010/geo/state-area.html |
| County Population & Geometry | US Census Bureau ACS via `tidycensus` (2022) |

## Key Findings
1. California dominates EV infrastructure with 17,797 stations, but coverage is heavily coastal — the Midwest is largely underserved.
2. New Jersey and Nevada have the highest EV-to-charger ratios, indicating demand significantly outpacing supply.
3. Only Colorado, Massachusetts, and California have more EV stations than gas stations; every other state still has a substantial gap relative to mature gasoline infrastructure.
4. **Texas, Florida, New Jersey, and Illinois** are the most underbuilt states relative to their expected needs based on registrations and population.
5. Pennsylvania shows strong coverage in the east and west but meaningful rural gaps in mid-state regions at 5- and 10-mile buffer distances.

## Modeling Approach
EV registrations and population were selected as the primary predictors of charging infrastructure need. The station gap — expected minus actual station count — serves as the primary measure of infrastructure need.

## Coverage Analysis
Coverage maps were generated for the most underbuilt states and Pennsylvania using a custom R function (`analyze_ev_coverage`). EV stations were spatially buffered at 1, 5, and 10 miles and overlaid on county-level population density to distinguish rural coverage gaps from true infrastructure deficits.

## Next Steps
- Add highways and urban centers as context layers in coverage maps
- Incorporate geospatial features into modeling (highway proximity, urban density)
- Apply cross-validation to strengthen predictions given the small state-level sample size
- Benchmark US state metrics against Norway as a global EV leader