# Airbnb NYC Business Intelligence

Clustering and exploratory analysis of New York City Airbnb listings. The work segments the market by price, service fee, stay length, review volume, and location, then turns those segments into business observations and recommendations.

This repository is written for researchers, analysts, and hiring reviewers who want a complete, reproducible view of the study: the data, the notebooks, and the written outcome.

## What this repository answers

- Which room types dominate NYC supply, and which ones carry a price premium?
- How concentrated is inventory across boroughs?
- Do price, fees, reviews, and location form distinct market segments?
- Where should supply strategy focus if the goal is to ease price pressure?

## Repository layout

| Path | Purpose |
| --- | --- |
| `data/Airbnb_Data.csv` | NYC listings used by both notebooks (about 102,600 rows, 26 columns) |
| `Project1.ipynb` | Primary analysis notebook: cleaning, charts, elbow method, K-means, silhouette score |
| `Airbnb_code.ipynb` | Companion notebook with the same analytical pipeline |
| `OUTCOMES.md` | Observations, analysis, and recommendations |
| `Analysis.md` | Chart-led analysis of supply, price, and the three clusters |
| `figures/` | Charts used in `Analysis.md` |
| `requirements.txt` | Python packages needed to rerun the notebooks |

## Method

1. Clean missing values. Numeric fields use the mean or the mode. Text fields use explicit placeholders. A small number of missing neighbourhood labels are filled from latitude and longitude with GeoPy (OpenStreetMap Nominatim).
2. Explore room type, price, minimum nights, and borough supply with histograms, box plots, heatmaps, and bar charts.
3. Scale price, minimum nights, number of reviews, service fee, latitude, and longitude.
4. Choose **K = 3** with the elbow method and fit K-means (`random_state=0`).
5. Check separation with the silhouette score and map the clusters.

## Headline results

Figures below come from the executed notebook outputs.

**Room type supply**

| Room type | Listings | Share |
| --- | ---: | ---: |
| Entire home/apt | 53,699 | 52.34% |
| Private room | 46,550 | 45.37% |
| Shared room | 2,226 | 2.17% |
| Hotel room | 116 | 0.11% |

**Average listed price**

| Room type | Average price |
| --- | ---: |
| Hotel room | 668.47 |
| Shared room | 634.10 |
| Entire home/apt | 625.08 |
| Private room | 625.03 |

**Borough supply (share of listings)**

| Borough | Share |
| --- | ---: |
| Manhattan | 42.69% |
| Brooklyn | 40.80% |
| Queens | 12.93% |
| Bronx | 2.64% |
| Staten Island | 0.93% |

Manhattan and Brooklyn together hold about 83% of listings. Hotel rooms are rare and the most expensive room type. K-means with three clusters has a silhouette score of **0.55**. The most common neighbourhoods inside the clusters are Bedford-Stuyvesant (clusters 0 and 1) and Williamsburg (cluster 2). Average availability is about 140 days in every cluster, and average reviews per month sit near 1.15 to 1.17.

The full interpretation, including the course recommendation, is in [OUTCOMES.md](OUTCOMES.md).

## How to run

Python 3.10 or newer is expected. Pandas 3 changes column `fillna(..., inplace=True)`, so this project stays on pandas 2.2.

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS or Linux
source .venv/bin/activate

pip install -r requirements.txt
jupyter notebook
```

Open `Project1.ipynb` or `Airbnb_code.ipynb` and run all cells. Each notebook searches upward from the working directory for `data/Airbnb_Data.csv`, so the same files run on any machine. No local drive path is required.

Two cells call the public Nominatim geocoder for rows that are missing a neighbourhood. That step needs a network connection and is rate-limited. If a lookup fails, the notebook records `Unknown` and continues.

The map cell builds a Folium marker for every listing. On a full run that file is large and slow. The clustering tables do not depend on the map.

## Data notes

- Column 25 (`license`) is mostly empty and is stored as mixed types. The notebooks read it with `low_memory=False` in `Project1.ipynb`.
- A few borough labels are spelling variants (`brookln`, `manhatan`, `The Bronx`, `Queens County`). Those rows are rare and pull some borough averages, especially `The Bronx` at 909.
- Prices and service fees are cleaned from currency text, then missing numbers are filled with the column mean.
- This is a course study (DS 514 Data Mining), not an official Airbnb dataset release. Listings, prices, and availability describe the snapshot in `data/Airbnb_Data.csv`.

## Suggested reading order

1. [Analysis.md](Analysis.md) for the charts and the reading of each result.
2. [OUTCOMES.md](OUTCOMES.md) for the business narrative.
3. `Project1.ipynb` for the charts, elbow plot, cluster tables, and silhouette score.
4. `Airbnb_code.ipynb` if you want the parallel implementation.
