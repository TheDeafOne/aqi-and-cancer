# Air Quality and Cancer

This project explores county-level air quality and cancer incidence in the United States using public data from 2016–2020. It brings the two datasets together in a Jupyter notebook, using maps, distributions, and simple comparisons to look for regional patterns.

![Number of records in each included annual county AQI file: 1,030 in 2016, 1,029 in 2017, 1,021 in 2018, 1,020 in 2019, and 1,003 in 2020](docs/aqi-data-coverage.svg)

*An inventory of the included AQI files. These are reporting records, not a measure of air quality or complete coverage of every county. [Counts as CSV](docs/aqi-data-coverage.csv).*

## Motivation

Air quality and cancer incidence both vary across the country. Public datasets make it possible to explore that variation, but comparing them takes more than putting two maps next to each other. Locations need to match, rates and counts mean different things, and a shared time window does not necessarily describe a shared exposure history.

The goal was to work through that comparison: clean the data, make the geography visible, and examine what a simple relationship between the two datasets might look like. The notebook is an exploratory study rather than a model of individual cancer risk.

## Questions

- How do cancer incidence rates and reported trends vary by county and region?
- How do AQI summaries and pollutant-specific day counts vary across the same period?
- What happens when the two datasets are compared at the county level?
- Does a simple linear model describe much of the variation in cancer incidence?

## Data

| Dataset | Included files | What it provides |
| --- | --- | --- |
| Annual county AQI summaries | [2016](data/annual_aqi_by_county_2016.csv), [2017](data/annual_aqi_by_county_2017.csv), [2018](data/annual_aqi_by_county_2018.csv), [2019](data/annual_aqi_by_county_2019.csv), [2020](data/annual_aqi_by_county_2020.csv) | Median and maximum AQI, observation days, AQI-category days, and pollutant-specific day counts |
| Cancer incidence report | [2016–2020 report](data/cancer_incd_rate_2016_2020.csv) | Age-adjusted incidence per 100,000 people, average annual case counts, confidence intervals, and reported trends |
| County lookup | [County table](data/uscounties/uscounties.csv) | FIPS codes, coordinates, state names, and population |

The AQI summaries follow the EPA [AirData](https://www.epa.gov/outdoor-air-quality-data) format. The cancer file is a [State Cancer Profiles](https://statecancerprofiles.cancer.gov/) export covering all cancer sites and stages, all races, both sexes, and all ages. Its footer contains the source and suppression notes, including SEER and National Program of Cancer Registries attribution. The original download source for the county lookup is not recorded in the repository.

The repository also contains daily lead-monitoring files for 2016–2020. The main notebook uses the annual AQI summaries rather than those daily files.

## Exploring the notebook

Open [aqi_cancer_eda.ipynb](aqi_cancer_eda.ipynb) to read the analysis and its saved output. It has four main parts:

1. **Data preparation:** convert numeric fields, handle suppressed or unavailable values, and attach county geography.
2. **Cancer exploration:** compare incidence rates and trends, inspect their distributions, and map regional variation.
3. **AQI exploration:** combine annual summaries and visualize air-quality measures geographically.
4. **Comparison:** join the datasets, inspect scatter plots and correlations, and fit a linear regression using median AQI to explain cancer incidence.

The implementation uses pandas and NumPy for data preparation, Plotly for interactive figures, and scikit-learn for the regression. Hover details in the Plotly figures make it possible to inspect individual counties alongside the broader pattern.

## Reading the results

The saved regression has an in-sample R² of approximately **0.00027**. That fit describes almost none of the variation in its input data, but it is not a validated estimate of the relationship between pollution and cancer.

The current comparison has a few important limits:

- The AQI geography helper joins on county name alone. Counties with the same name in different states can be conflated; this needs a validated state/county or FIPS join before drawing conclusions from the combined data.
- The trend merge repeats county summaries across annual percentage-change rows. A follow-up analysis needs to define one row per county or use an explicit county-year model.
- Monitoring coverage varies, cancer data can be suppressed, and population, exposure timing, and other influences need to be considered. County-level associations also do not establish individual risk or causation.

The notebook is useful as an exploration of the data and the decisions involved in comparing it. Its maps and model output should be read with those limits in mind.

## Working locally

The notebook records Python 3.11. Use that version for a local environment, and run JupyterLab from the repository root so the relative `data/` paths resolve.

```sh
git clone https://github.com/TheDeafOne/aqi-and-cancer.git
cd aqi-and-cancer
python3.11 -m venv .venv
```

Activate with `source .venv/bin/activate` on macOS/Linux, or `.venv\Scripts\Activate.ps1` in Windows PowerShell. Then install the notebook tools and imported packages:

```sh
python -m pip install "numpy<2" "pandas==1.5.3" plotly scikit-learn jupyterlab nbformat
python -m jupyter lab
```

Open `aqi_cancer_eda.ipynb`. The pandas pin retains the older grouping and correlation behavior used by the notebook; the original run did not record a complete package lock. The included CSV files are sufficient to inspect the saved analysis without downloading new data.
