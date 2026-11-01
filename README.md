# ICE in Maryland: data analysis

The analysis behind a three-story Capital News Service series by Mehedi Marof on U.S. Immigration and Customs Enforcement in Maryland:

1. [ICE's arrests in Maryland nearly doubled this year. The growth is people with no criminal record](https://mehedi-marof.github.io/ICE-arrests-in-maryland-nearly-doubled/)
2. [After an ICE arrest in Maryland, most people were held out of state. Many were moved again and again](https://mehedi-marof.github.io/After-ICE-arrests-in-Maryland/)
3. [Maryland's 287(g) ban and aftermath](https://mehedi-marof.github.io/maryland-police-can-still-help-ICE/)

Every number in the stories comes from `maryland_ice_notebook.qmd`. Each story footnote names the notebook section it comes from (for example, "Notebook section 2.4").

## The data

ICE arrest, detainer and detention records released through Freedom of Information Act lawsuits and published by the [Deportation Data Project](https://deportationdata.org/data/processed/ice.html) (UC Berkeley and UCLA). We use the release of Aug. 24, 2026, covering Oct. 1, 2022, through Aug. 6, 2026. See `data/raw/README.txt` for the files and where to get them.

**Two time periods.** Year-over-year comparisons use January–July 2025 vs. January–July 2026. Everything else covers Jan. 20, 2025, when President Donald Trump's second term began, through Aug. 6, 2026.

## Files

```
maryland_ice_notebook.qmd    the analysis (R, Quarto)
maryland_ice_notebook.html   the notebook with every table and result, for reading without running it
csv/                         tables the notebook writes for the graphics (Part 5)
data/lookups/                our lookup tables: city to county, 2020 county population, map coordinates
data/raw/                    the ICE files go here (see README.txt)
```

## Run it

R with the `tidyverse`, `janitor`, `lubridate` and `arrow` packages. Put the parquet files in `data/raw/`, open the project folder as the working directory and render the notebook in RStudio or with `quarto render maryland_ice_notebook.qmd`. It takes under a minute.

## What's in the notebook

| Part | File | Sections |
|---|---|---|
| 1 | Arrests | 1.5 growth by criminal history, Jan.–July 2025 vs. 2026 · 1.6 since Jan. 20, 2025 · 1.7 no-record share over time · 1.8 custodial vs. non-custodial · 1.9 before and after the 287(g) ban · 1.10–1.12 counties, Eastern Shore, ICE offices · 1.13 citizenship · 1.14 national comparison |
| 2 | Detainers | 2.1 facility types · 2.2 per month around the ban · 2.3 outcomes · 2.4 outcomes by jail |
| 3 | Arrests joined to detention | 3.1 Baltimore hold room · 3.2 who was detained · 3.3 outcomes of 2025 arrests (no record, and everyone) · 3.3b by country · 3.4 where people were sent |
| 4 | Detention stints | 4.1 facilities per person · 4.1b held in the Fifth Circuit · 4.2 paths through detention |
| 5 | | writes the tables in `csv/` |

## Tables for the graphics (`csv/`)

| File | Graphic | Story |
|---|---|---|
| growth.csv | Arrests by criminal history, Jan.–July | 1 |
| cities.csv, counties.csv | Arrest map (scroll) | 1 |
| states.csv | No-record share by state | 1 |
| share_monthly.csv | No-record share by month, Maryland vs. U.S. | 1 |
| holdroom.csv | Baltimore hold room stays | 2 |
| facility_counts.csv | Facilities per person | 2 |
| facilities.csv, route.csv | Detention map and route (scroll) | 2 |
| citizenship_arrests.csv | Arrests by citizenship | 2 |
| destinations.csv, routes.csv, countries.csv | Where people were sent (globe, scroll) | 2 |
| citizenship.csv | Citizenship with departures (not currently used in a story) | — |
| g287.csv, detainers.csv | 287(g) arrests and detainers by month | 3 |
| detainer_outcomes.csv | Detainer outcomes by jail | 3 |
| custody.csv | Custodial vs. non-custodial arrests by month | 3 |

## Limits

The records show what ICE recorded, not why. They don't show immigration status, why someone was moved, or where people arrested lived. ICE keeps updating recent months, so the latest figures may rise. Facility types (county jail, state prison and so on) were classified by CNS from ICE's facility names.

## Credit

Analysis: Mehedi Marof, Capital News Service, Philip Merrill College of Journalism, University of Maryland. Data: Deportation Data Project.
