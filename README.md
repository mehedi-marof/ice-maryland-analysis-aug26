# ICE in Maryland: data analysis

The analysis behind a three-story Capital News Service series by Mehedi Marof on U.S. Immigration and Customs Enforcement in Maryland:

1. [ICE's arrests in Maryland nearly doubled this year. The growth is people with no criminal record](https://mehedi-marof.github.io/ICE-arrests-in-maryland-nearly-doubled/)
2. [After an ICE arrest in Maryland, most people were held out of state. Many were moved again and again](https://mehedi-marof.github.io/After-ICE-arrests-in-Maryland/)
3. [Maryland's 287(g) ban and aftermath](https://mehedi-marof.github.io/maryland-police-can-still-help-ICE/)

Every figure the stories draw from the ICE records comes from `maryland_ice_notebook.qmd`. Each story footnote names the notebook section it comes from (for example, "Notebook section 2.4"). Other facts in the stories, such as court rulings, government statements and county records, come from the sources named in the stories.

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
| 4 | Detention stints | 4.1 facilities per person · 4.1b held in Louisiana, Texas and Mississippi · 4.2 paths through detention |
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

## How we did this analysis

### Where the records come from

ICE does not publish these records itself. The Deportation Data Project, a research group at the University of California, Berkeley, and UCLA, obtained them through Freedom of Information Act lawsuits and posts them on its [data page](https://deportationdata.org/data/processed/ice.html). We used the release of Aug. 24, 2026, which covers Oct. 1, 2022, through Aug. 6, 2026.

We used four of the project's files:

- **Arrests:** one row for each arrest ICE made.
- **Detainers:** one row for each request ICE sent to a jail or prison.
- **Arrests linked to detention:** one row for each arrest, with the person's time in ICE detention attached.
- **Detention stints:** one row for each stop a person made at a detention facility.

The same event sometimes appears in a file more than once. The project marks rows it believes are duplicates (in a column called `duplicate_drop_row`), and we removed them from every file.

### Time periods

When we compare this year with last year, we use the same months in both years, January through July, because the records stop on Aug. 6, 2026. Everything else covers Jan. 20, 2025, the day President Donald Trump's second term began, through Aug. 6, 2026, unless the story says otherwise.

Maryland banned 287(g) agreements on Feb. 17, 2026. To compare before and after the ban, we had two periods of different lengths: about 13 months before (Jan. 20, 2025, to Feb. 16, 2026, or 393 days) and about five and a half months after (Feb. 17 to Aug. 6, 2026, or 171 days). Comparing raw totals would be misleading, so we turned each into a monthly average: the count divided by the number of days, times 30.4, the average number of days in a month.

### Arrests (notebook Part 1)

**Which arrests are in Maryland.** ICE records the state of each arrest, but sometimes leaves it blank. The Deportation Data Project fills in those blanks from the arrest location, in a column called `apprehension_state_filled_in`. Using that column finds 12,859 Maryland arrests, 214 more than ICE's own state field. After removing 218 likely duplicates, 12,641 remained.

**Arrests, not people.** Some people were arrested more than once, and 28 arrests have no person ID. So the stories count arrests, not people, unless they say otherwise.

**Criminal history.** ICE puts each person it arrests into one of three groups, based on what its systems showed at the time of the arrest (`apprehension_criminality`):

- "Convicted Criminal": had a prior criminal conviction;
- "Pending Criminal Charges": had a criminal charge that had not been resolved;
- "Other Immigration Violator": neither. The stories describe this group as people with no criminal record, meaning no prior conviction or pending charge in ICE's systems. It does not mean they were in the country legally.

**How the arrest happened.** ICE records how each arrest was made (`apprehension_method_simple`). We grouped them into two kinds:

- **Custodial arrests:** the person was already held by a jail, prison or other law enforcement agency, which handed them to ICE. This includes arrests ICE codes "Custodial Arrest," including its Criminal Alien Program, and "287(g) Program."
- **Non-custodial arrests:** ICE found the person itself, at a home, workplace, roadside or one of its own offices. Most are coded "At-Large Arrest."

Arrests at ICE's two Baltimore offices, its field office and the office where immigrants under supervision report for check-ins, were identified by the location ICE recorded (`event_landmark`). We counted an arrest as a 287(g) arrest only if ICE itself coded it that way.

**Comparisons with the rest of the country.** National figures come from the same arrests file, using all states. When we ranked states by the share of arrests involving people with no record, we included only the 43 states and territories with at least 1,000 arrests since Jan. 20, 2025, because shares based on small numbers can swing widely. We left out the District of Columbia.

### Places

ICE usually records a city or general area, not an exact address. To place each arrest in a county, we first looked at whether ICE's location description named a county or Baltimore City. If it did not, we used the city ICE recorded and matched it to a county using a lookup table we built (`data/lookups/md_city_to_county.csv`). The city names in the records are often misspelled or abbreviated, so we matched most to a ZIP code directory, corrected others by hand and left a few unplaced. In all, 98.7% of arrests, and 99.5% of those since Jan. 20, 2025, were placed in a county.

To compare counties of different sizes, we calculated arrests for every 10,000 residents, using 2020 census populations from the Maryland Department of Planning. These rates count all residents, not only immigrants, and they leave out arrests at ICE's two Baltimore offices, because those are recorded at the office's location rather than where the person was found. The Eastern Shore means Caroline, Cecil, Dorchester, Kent, Queen Anne's, Somerset, Talbot, Wicomico and Worcester counties. On the map, each circle sits at the center of the city ICE recorded.

### Detainers (notebook Part 2)

A detainer is a written request from ICE asking a jail or prison to hold someone for up to 48 hours past their release, or to notify ICE before letting them go, so agents can pick them up. Jails are not required to comply.

We kept detainers sent to facilities in Maryland and dated each one by the day ICE prepared it. ICE does not say what kind of facility each one is, so we sorted the facility names into groups (county jails, state prisons and state-run jails, federal custody, police or courts, and ICE offices) using words in the names, such as "detention center" or "correctional."

ICE records how each detainer ended (`detainer_lift_reason`). "Booked into Detention" means ICE took the person into custody; "Detainer Declined by LEA" means the local jail or agency declined the request. Some detainers were closed because they were duplicates or were replaced by a new form; we left those out of the outcome counts so the same request isn't counted twice. About half of the remaining detainers have no recorded outcome yet.

For the chart of outcomes by jail, we used seven county jails and sorted every detainer since Jan. 20, 2025, into three groups: ICE took custody, the jail declined, or other or not yet recorded.

### Detention and departures (notebook Part 3)

This file links each arrest to the person's time in ICE detention. Because each row is an arrest, a person detained after two arrests appears twice: the 8,319 detention stays involve 8,279 people. When the stories count people, each person is counted once.

**The Baltimore hold room.** For people whose first stop was the hold room at ICE's Baltimore office, we measured the hours between when they were booked in and booked out. For each month, we report the median, the middle value: half the people stayed longer and half stayed shorter. The median is less affected by a few unusually long stays than an average would be. People still in the room when the records end have no book-out time and are not counted, so the most recent month may understate stays.

**How cases ended.** A departure means the records show a date the person left the U.S., either deported or by voluntary departure. The records also show whether someone was released or is still detained. Cases can take months to resolve, and the records end in early August 2026, so to describe how cases ended we used arrests made from Jan. 20 through Dec. 31, 2025, which have had at least seven months to play out.

**Sent to another country.** We counted a person as sent to a country other than their own when the country they were sent to differs from their citizenship in ICE's records. One record listing "KOREA" and "SOUTH KOREA" was treated as the same country. These counts cover people arrested since Jan. 20, 2025. The arrests file records slightly fewer departures than the detention file (7,536 compared with 7,611), so totals can differ slightly depending on which file is used. On the globe, each circle sits at the center of a country, not at the place the person arrived.

### Facilities and paths (notebook Part 4)

This file has one row for every stop a person made at a detention facility. We kept the stops for the people above.

**How many facilities.** We counted the number of different facilities each person passed through, using ICE's code for each facility.

**Where people were held.** Time held in Louisiana, Texas, Mississippi and at Guantánamo Bay comes from the state ICE recorded for each facility, where Guantánamo is coded "CU."

**Paths.** To trace each person's path, we put their stops in order by the time they were booked in and combined back-to-back stops at the same facility. The map shows the most common path, which 360 people followed, and the most common path with five stops, which 25 people followed. Times on that route are medians for the group, not any one person's record. Facilities are placed at the center of the city where they are located.

### What the records can't tell us

- The records show how an arrest happened, not why ICE made it.
- "No record" reflects what ICE's systems showed at the time of the arrest, which can be incomplete. The records do not show immigration status.
- ICE often records a general area rather than an address, so arrests were placed by the city or county ICE listed. The records don't show where the people arrested lived.
- About half of the detainers have no recorded outcome yet, so outcome shares could change. The records cannot show informal contacts between local officers and ICE.
- The records show where and when someone was held, not why ICE moved them. Hold room times show how long people stayed, not how crowded the rooms were. Where someone was held does not show where their case was heard.
- A departure is recorded as a removal or a voluntary departure; the records don't show how that decision was made. Destinations are as ICE recorded them.
- Facility types were sorted by CNS from ICE's facility names and may not be exact.
- ICE keeps updating its records, so counts for the most recent months may grow, and paths for people still detained may be incomplete.

## Credit

Analysis: Mehedi Marof, Capital News Service, Philip Merrill College of Journalism, University of Maryland. Data: Deportation Data Project.
