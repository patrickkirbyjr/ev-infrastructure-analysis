# EV Infrastructure Analysis

## Summary
This project analyzes the distribution of electric vehicle (EV) charging stations across the United States to identify which states are most in need of additional charging infrastructure. Publicly available datasets on EV stations, EV registrations, state populations, gas stations, and state geographic areas were sourced from the US Department of Energy and US Census Bureau. Statistical modeling — including linear regression, random forest, and gradient boosting — was used to predict a **station gap** (expected stations minus current stations) for each state, identifying where infrastructure is relatively underbuilt or overbuilt. Location data from all EV stations across the continental US were then used to visualize spatial coverage within specific radii for the most underbuilt states.

## Project Structure
The project is organized across four R Markdown notebooks:

| Notebook | Description |
|---|---|
| `ev_analysis_preprocessing.Rmd` | Data ingestion, cleaning, joining, and derived metric calculation |
| `ev_analysis_eda.Rmd` | Exploratory visualizations including bar charts and choropleth heat maps |
| `ev_analysis_modeling.Rmd` | Statistical modeling and station gap prediction |
| `ev_analysis_geospatial.Rmd` | Spatial coverage analysis for underbuilt states |

## Project Goals
1. Acquire, clean, and join data on EV stations, registrations, population, gas stations, and state areas
2. Explore the current US EV charging landscape through visualizations of key infrastructure metrics
3. Use statistical modeling to identify the states that are most underbuilt and overbuilt relative to their expected infrastructure needs
4. Conduct geospatial analysis to determine the coverage area for underbuilt states at multiple buffer distances

## Data Sources

| Dataset | Source |
|---|---|
| EV Station Data | https://afdc.energy.gov/data_download |
| EV Registration Data | https://afdc.energy.gov/data/10962 |
| State Population Data | https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html |
| Gas Station Data | https://afdc.energy.gov/files/u/data/data_source/10333/10333_gasoline_stations_year.xlsx |
| State Area Data | https://www.census.gov/geographies/reference-files/2010/geo/state-area.html |
| County Population & Geometry | US Census Bureau ACS via `tidycensus` (2022) |

## Findings

### Exploratory Analysis
1. California has, by far, the most EV stations with 17,797 in total. EV coverage is denser along the coasts and sparse across the Midwest.
2. New Jersey and Nevada have the highest ratio of EVs to available charging stations, suggesting high demand relative to supply.
3. Louisiana and Mississippi have the highest ratio of people to available charging stations.
4. California has the highest EV saturation by population, followed by other Pacific Coast states.

### Modeling
5. A linear model using EV registrations and population as predictors was the most statistically effective approach. Random forest and gradient boosting were tested but produced lower explained variance on this dataset.
6. EV registrations and population are strong predictors of EV infrastructure. Gas stations and state geographic area are not statistically significant predictors, though including area reduces extreme predictions for large states.
7. Gas stations were used as a benchmark for comparison: the average ICE driver does not worry about fuel availability, so mature gasoline infrastructure serves as a reference point for what adequate EV coverage could look like. Only Colorado, Massachusetts, and California currently have more EV stations than gas stations.
8. Models identify **Texas, Florida, New Jersey, and Illinois** as the most underbuilt states in terms of EV charging infrastructure relative to their expected needs (station gap).

### Geospatial Analysis
9. Pennsylvania, which models consider slightly underbuilt, has strong EV station coverage in the east and west but notable gaps in rural mid-state areas at the 5- and 10-mile buffer distances.
10. Coverage maps overlaid on county-level population density confirm that most uncovered areas in underbuilt states correspond to lower-density rural regions.

## Modeling Approach
The station gap — the difference between a model's expected station count and a state's actual station count — was used as the primary measure of infrastructure need. Four feature combinations were tested across linear regression, random forest (`randomForest`), and gradient boosting (`gbm`) frameworks:

- Registrations + Population
- Registrations + Population + Gas Stations
- Registrations + Population + Area
- Registrations + Population + Gas Stations + Area

Model comparisons used ANOVA for nested linear models and permutation-based feature importance (`rfPermute`) for ensemble models. The linear model using registrations, population, and state area produced the most interpretable and stable results.

## Coverage Maps
Coverage maps were generated for states identified as the most underbuilt, as well as Pennsylvania. Using a custom R function (`analyze_ev_coverage`), EV stations were spatially buffered at 1, 5, and 10 miles to represent coverage zones ranging from highly convenient to slightly inconvenient. Maps are overlaid on county-level population density to distinguish rural coverage gaps from true infrastructure deficits.

## Next Steps
- Include major highways and cities as context layers in coverage maps
- Add additional geospatial features to modeling (proximity to highways, urban density, EV ownership rates)
- Incorporate model cross-validation to strengthen station gap predictions given the small state-level sample size
- Compare US state infrastructure metrics against Norway as a global EV leader benchmark