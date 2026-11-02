# Weather and Air Quality in European Capitals: an Integrated Data Pipeline

Does the weather affect the air we breathe?
This project builds an end-to-end **data pipeline** that collects meteorological and air pollution data for **21 European capitals** from two public APIs, integrates them, checks their quality and stores them in **MongoDB** for analysis.

> Group project for the *Data Management* course — MSc in Data Science, University of Milano-Bicocca (2025).

---

## Pipeline

```
WeatherAPI ─┐                                                     daily JSON
            ├──> Acquisition ──> Quality ──> Integration ──> Enrichment ──> files ──> MongoDB ──> Queries
IQAir API ──┘    (daily script)  checks      (date → city    (EPA AQI       (merged)
                                              → details)      categories)
```

1. **Acquisition** — the free API plans do not provide historical data, so a Python script runs **every day** and queries both APIs for each of the 21 capitals, building the historical dataset progressively. Controlled delays and retries handle API rate limits and failed calls.
2. **Data quality** — each record is validated before being kept: presence of all required fields, numeric types, and values within realistic physical ranges. Incomplete or inconsistent records are discarded.
3. **Integration** — weather and air-quality data are merged into one document per date, with the hierarchy `date → city → details`, removing redundant fields (e.g. repeated city names and timestamps).
4. **Enrichment** — the numeric AQI is mapped to the EPA categories (*Good*, *Moderate*, *Unhealthy for Sensitive Groups*, *Unhealthy*, *Very Unhealthy*, *Hazardous*).
5. **Storage** — daily JSON files are merged and loaded into **MongoDB**, a document database well suited to this semi-structured data.
6. **Analysis** — queries wrapped in Python functions for easy reuse.

---

## Data

| Source | Variables |
|---|---|
| [WeatherAPI](https://www.weatherapi.com/) | temperature (°C), weather condition, humidity (%), wind speed (km/h) |
| [IQAir AirVisual](https://www.iqair.com/) | US Air Quality Index (AQI), main pollutants, region, country |

Both APIs require a free API key (not included in this repository).

---

## Queries

| # | Question |
|---|---|
| 1 | What was the air quality in a given city on a given day? |
| 2 | What is the correlation between temperature and AQI across the 21 capitals on a given day? |
| 3 | Which are the 5 windiest cities of the day, and what is their AQI? |
| 4 | Which capitals had the worst air quality on a given day? |

---

## Repository structure

```
├── notebooks/
│   └── data_management_project.ipynb   # acquisition, quality checks, integration, MongoDB, queries
├── report/
│   └── report.pdf
└── README.md
```

---

## Limitations

- **Short collection period**: data was collected for only a few weeks (spring 2025), so the dataset is too small for reliable conclusions on the weather–air quality relationship. The project focuses on the **pipeline**, not on the findings.
- **Cross-sectional correlations**: the temperature–AQI correlation is computed across 21 cities on a single day, so it reflects differences between cities rather than the effect of weather over time.
- **One snapshot per day**: daily measurements do not capture variation within the day.

**Next steps:** a scheduled collection over several months, more sources (traffic, monitoring stations) and predictive models of air quality.

---

## Tools

Python · requests · MongoDB · PyMongo · NumPy · REST APIs · JSON

---

## Authors

- **Carlo Pegoraro** — [GitHub](https://github.com/carlopego14)
- **Davide Fabio Loreti**

Both authors worked jointly on all phases of the project: design of the pipeline, data acquisition, quality checks, MongoDB storage and queries.
