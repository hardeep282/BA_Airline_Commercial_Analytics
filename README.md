# BA Airline Commercial Analytics

An end-to-end commercial analytics project built around airline route competitiveness, fare forecasting, and competitor intelligence — combining SQL Server data engineering, machine learning, time-series forecasting, and a RAG-powered chat assistant, all deployed as a live interactive dashboard.

**Live app:** [https://ba-airline-commercial-analytics.streamlit.app/](https://ba-airline-commercial-analytics.streamlit.app/)
**Note:** the app may take a few seconds to wake up if it hasn't been visited recently (Streamlit Community Cloud free-tier behavior).

## What this project does

Airlines and analysts need to understand not just how a route performs operationally, but how that performance compares against pricing and competitive pressure. This project combines two independent public datasets — flight-level operational data and route-level fare/market-share data — into a single, queryable view of route competitiveness, then layers forecasting and AI-assisted competitor research on top.

## Architecture

- **Data sources:** BTS TranStats 2024 flight operations data (~7M rows) and DOT DB1B route market fare data (1993-2024), both public US federal transportation data
- **Database:** SQL Server, structured in a bronze/silver/gold layered architecture — raw ingested data, cleaned per-route views, and a final joined competitive-performance view (1,274 routes with same-year operational and fare data)
- **Machine learning:** K-Means clustering (k=5, selected via silhouette score) segmenting routes into commercially meaningful groups — including a standout "Premium Dominant, Service-Strained" cluster of 177 routes charging premium fares without matching service reliability
- **Forecasting:** ETS (exponential smoothing) fare forecasting, with an honest dual-mode comparison between pre-COVID accuracy and accuracy including pandemic-era disruption, since a single number would misrepresent real-world reliability
- **RAG / competitor intelligence:** a retrieval-augmented chat assistant answering questions about real 2024-2026 competitor developments (Southwest's governance restructuring, Spirit Airlines' 2026 shutdown, JetBlue's premium pivot), grounded in a small curated document corpus via ChromaDB and Claude
- **App:** Streamlit, deployed both via Docker (for local/containerized use) and Streamlit Community Cloud (for the public live link)

## Key findings

- A distinct cluster of 177 routes ("Premium Dominant, Service-Strained") charge high fares (~$291 avg) with high carrier dominance (77% market share), yet show the worst average arrival delay (19.4 min) of any cluster — a real, actionable pricing-vs-service gap
- Fare forecasting accuracy varies meaningfully depending on whether the test period includes COVID-era disruption (e.g. DEN-HOU: 8.2% MAPE pre-COVID vs 13.8% including 2020-2024) — the app surfaces both, rather than one misleading number
- Spirit Airlines' actual 2026 shutdown is reflected in the competitor intelligence corpus, since Spirit appears as a low-cost competitor across many of the dataset's routes — a real-world event with direct relevance to route-level competitive assumptions

## Cluster profiles

| Cluster | Name | Avg Fare | Market Share | Passengers | Avg Delay | Routes |
|---|---|---|---|---|---|---|
| 2 | Niche Monopoly Routes | $202.55 | 0.95 | 284.75 | 4.36 min | 446 |
| 4 | Major Hub Competitive Routes | $257.36 | 0.52 | 2270.19 | 9.31 min | 110 |
| 1 | Premium Dominant, Service-Strained | $290.62 | 0.77 | 341.19 | 19.36 min | 177 |
| 3 | Premium High-Demand | $344.93 | 0.60 | 564.45 | 3.76 min | 181 |
| 0 | Balanced Competitive | $198.80 | 0.56 | 548.11 | 6.36 min | 320 |

## Tech stack

Python, SQL Server, pandas, scikit-learn, statsmodels, ChromaDB, Anthropic API, Streamlit, Docker

## Running locally

```bash
cd app
pip install -r requirements.txt
streamlit run app.py
```

## Running via Docker

```bash
cd app
docker build -t ba-airline-app .
docker run -p 8501:8501 ba-airline-app
```

## Project structure

```
├── notebooks/        # Ingestion, forecasting, segmentation, RAG development
├── sql/               # Bronze/silver/gold SQL views
├── app/               # Streamlit app, Dockerfile, cached data
├── docs/              # Architecture notes
├── reports/figures/   # Cluster visualizations
```

## Data licensing

Both source datasets are derived from US federal government data (BTS TranStats, DOT DB1B), which carries no copyright restriction under US law. Original Kaggle uploads by Hrishit Patil and amitzala.
