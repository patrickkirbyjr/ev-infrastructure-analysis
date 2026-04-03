# EV Infrastructure Analysis: Market Saturation & Investment Gaps

## Executive Overview
This project evaluates the distribution of Electric Vehicle (EV) charging stations across the United States to identify critical infrastructure gaps and prioritize future market investments. By analyzing US Department of Energy and Census Bureau data, this analysis models the expected need for EV infrastructure based on current EV registrations, population density, and mature gasoline station footprints. Utilizing a Poisson regression model, the project quantifies the "station gap" for each state and translates these findings into spatial coverage maps to support operational decision-making.

## Analytical Workflow

| Phase | Notebook | Function |
|---|---|---|
| Data Integration | `ev_analysis_preprocessing.Rmd` | Merges DoE station locations, Census population demographics, and gasoline infrastructure data to calculate normalized metrics like EV-to-charger ratios. |
| Market Analytics | `ev_analysis_eda.Rmd` | Evaluates current usage patterns, highlighting states where demand significantly outpaces supply through exploratory visualizations and choropleth mapping. |
| Predictive Modeling | `ev_analysis_modeling.Rmd` | Deploys a Poisson regression model to quantify the "station gap" by comparing actual EV station counts against expected infrastructure needs. |
| Geospatial Insights | `ev_analysis_geospatial.Rmd` | Translates the modeling data into actionable coverage maps, applying 1-, 5-, and 10-mile radius buffers to visualize underbuilt areas against population density. |

## Data Sources
* **EV Station Data:** US Department of Energy (AFDC)
* **EV Registration Data:** US Department of Energy
* **State Population & Area Data:** US Census Bureau
* **County Population & Geometry:** US Census Bureau ACS via `tidycensus` (2022)
* **Gasoline Station Data:** US Department of Energy (AFDC)

## Core Business Insights
* **High-Priority Investment Markets:** Texas, Florida, New Jersey, and Illinois exhibit the largest deficit in EV stations relative to their expected needs based on registrations and population, making them prime targets for expansion.
* **Demand Outpacing Supply:** New Jersey and Nevada currently have the highest EV-to-charger ratios in the country, indicating immediate saturation and a pressing need for infrastructure development to support existing drivers. 
* **Coverage vs. Density (Pennsylvania Focus):** While Pennsylvania shows strong coverage in its eastern and western hubs, 5- and 10-mile spatial buffers reveal meaningful gaps in mid-state regions, highlighting a strategic opportunity to capture inter-city transit routes.
* **The Gasoline Benchmark:** Only Colorado, Massachusetts, and California have more EV stations than gas stations. The remainder of the US market still has a substantial gap to close relative to mature gasoline infrastructure.

## Technical Stack
* **Languages & Environments:** R, RStudio, RMarkdown
* **Data Engineering & Analysis:** `tidyverse`, `dplyr`, `janitor`
* **Geospatial & Visualization:** `ggplot2`, `sf`, `tidycensus`
* **Modeling:** Base R (`glm` with Poisson family/log link)

## Strategic Next Steps
* Automate data ingestion of the DoE's Alternative Fueling Station Locator API to track infrastructure performance variance and market shifts on a quarterly basis.
* Incorporate geospatial highway proximity and urban density metrics into the modeling phase to refine site-selection prioritization.
* Apply cross-validation techniques to strengthen predictive modeling accuracy across state-level data.