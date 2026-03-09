# World Values Survey Explorer

An interactive web application for exploring and visualizing data from the [World Values Survey](https://www.worldvaluessurvey.org/) (WVS) — covering **108 countries** across **7 survey waves** (1981–2022).

## Features

- **Interactive World Map** — Choropleth map colored by any selected metric. Click a country to drill down.
- **7 Thematic Categories** — Demographics, Values & Happiness, Trust & Institutions, Politics, Social & Cultural, Moral Views, Welzel Indices.
- **42 Metrics** — Each with full descriptions explaining the survey question and scale.
- **Trend Charts** — See how a metric changed over survey waves for any country.
- **Response Distributions** — View how respondents in a country actually answered each question.
- **Country Comparison** — Compare up to 10 countries side-by-side with overlay line charts and Welzel values radar chart.
- **Wave Filter** — View data for a specific survey wave or the latest available.
- **Country Search** — Quickly find any country by name or ISO code.

## Project Structure

```
├── json/                  # Cleaned JSON data + preprocessed aggregated data
├── backend/
│   ├── main.py            # FastAPI application with REST API
│   └── requirements.txt   # Python dependencies
├── frontend/
│   ├── index.html         # Main HTML page
│   ├── css/style.css      # Dark-theme styles
│   └── js/
│       ├── app.js         # Main app logic, state, API calls
│       ├── map.js         # D3.js world map (choropleth + zoom/pan)
│       ├── charts.js      # Chart.js trend & distribution charts
│       └── compare.js     # Country comparison panel
├── preprocess.py          # Script to regenerate aggregated data in json/
├── Dockerfile             # Docker image definition
├── docker-compose.yml     # Docker Compose config
└── README.md
```

## Data Pipeline

Our data went through three stages of processing to go from a massive raw dataset to a fast, lightweight web app:

### Step 1: Raw Data — 32 GB

The original dataset (`dataset.jsonl`) from the [World Values Survey](https://www.worldvaluessurvey.org/) is a 32 GB JSONL file containing **884,946 individual survey responses**, each with **726 columns** (questions). Every row is one person's answers to hundreds of questions about their values, beliefs, and demographics — collected across 108 countries and 7 survey waves (1981–2022).

### Step 2: Cleaned Data — ~900 MB (see `loading_json` branch)

The raw data was cleaned and reduced:
- **726 columns → ~6 per theme**: Only the most meaningful variables were kept, grouped into 7 themes (Demographics, Values & Happiness, Trust & Institutions, Politics, Social & Cultural, Moral Views, Welzel Indices).
- **Sentinel values removed**: Codes like `-1` ("Don't know"), `-2` ("No answer"), `-5` ("Not asked") were stripped out as they are not real answers.
- **Null fields stripped**: Empty fields were removed from JSON to save space.
- **Short key names**: Column names shortened (e.g. `country_code` → `cc`, `wave` → `w`) to reduce file size.
- Result: 7 themed JSON files totaling ~900 MB, stored in the `loading_json` branch.

### Step 3: Aggregated Data — ~2 MB (what the web app uses)

The `preprocess.py` script takes the 900 MB cleaned files and computes **statistical summaries**:
- Instead of storing 884,946 individual rows, it calculates **per-country, per-wave** statistics: mean, sample size (n), and full response distributions.
- Example: Instead of 12,000 individual German responses, we store one summary object per wave (~7 waves) with the average score and how many people gave each answer.
- This reduces 900 MB → ~2 MB while preserving everything needed for visualization (averages, trends, distributions).
- Output: the `json/` folder on the `main` branch (countries.json, themes.json, waves.json, and 7 per-theme aggregated files).

### Step 4: Backend + Frontend

- **Backend** (`backend/main.py`) — FastAPI server that loads the 2 MB aggregated data into memory on startup and serves it via REST API endpoints.
- **Frontend** (`frontend/`) — Pure HTML/CSS/JS app using D3.js for the interactive world map and Chart.js for trend/distribution charts. No build step required.

## Quick Start

### Option 1: Docker (Recommended)

```bash
docker compose up --build
```

Open [http://localhost:8000](http://localhost:8000) in your browser.

### Option 2: Run Locally

```bash
# Install dependencies
pip install -r backend/requirements.txt

# Start the server
uvicorn backend.main:app --host 0.0.0.0 --port 8000
```

Open [http://localhost:8000](http://localhost:8000) in your browser.

## API Endpoints

| Endpoint | Description |
|---|---|
| `GET /api/countries` | List of all 108 countries with codes |
| `GET /api/themes` | Available themes and their metrics |
| `GET /api/waves` | Survey wave numbers and year ranges |
| `GET /api/map/{theme}/{metric}?wave=N` | Map data (mean per country) |
| `GET /api/trend/{theme}/{metric}?countries=USA,DEU` | Trend over waves |
| `GET /api/distribution/{theme}/{metric}/{cc}?wave=N` | Response distribution |
| `GET /api/country/{cc}` | All data for one country |

## Technologies

- **Backend**: Python, FastAPI, Uvicorn
- **Frontend**: HTML5, CSS3, JavaScript (vanilla)
- **Visualization**: D3.js (world map), Chart.js (charts)
- **Deployment**: Docker

## Team

Data Wrangling and Visualization 2026 — Course Project
