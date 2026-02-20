# DWaV Project – WVS Data Visualization

Backend + Frontend application for interactive visualization of World Values Survey (WVS) data.

## The System Provides

- REST API for WVS data
- Interactive web interface with 3D visualization
- Docker-based deployment

## Project Structure

```
project/
├── backend/                 # FastAPI backend
│   ├── app/
│   │   ├── main.py
│   │   └── data/
│   │       └── wvs_data.json
│   ├── requirements.txt
│   └── Dockerfile
│
├── frontend/                # React + Vite frontend
│   ├── src/
│   ├── package.json
│   └── Dockerfile
│
├── docker-compose.yml
└── README.md
```

## Features

### Backend

- FastAPI REST API
- `/health` – service status
- `/data` – filter data by country, year, and region
- `/timeseries` – time series data for a country
- JSON-based data storage

### Frontend

- React + TypeScript
- 3D visualization (Three.js, React Three Fiber)
- Filters for country and year
- Connection test with backend
- Timeseries charts

## API Endpoints

### Health Check

```
GET /health
```

**Response:**
```json
{"status": "ok"}
```

### Get Data

```
GET /data?year=2017&country=Germany&region=Europe
```

Optional query params: `year`, `country`, `region`

**Example response:**
```json
[
  {
    "country": "Germany",
    "year": 2017,
    "region": "Europe",
    "values": {
      "happiness": 7.2,
      "trust": 0.61,
      "religiosity": 0.23,
      "life_satisfaction": 7.4
    },
    "embedding": { "x": 0.1, "y": 0.2, "z": 0.3 }
  }
]
```

### Get Timeseries

```
GET /timeseries?country=Germany
```

Returns year-over-year values for the specified country.

## Running Without Docker (Development)

### Backend

```bash
cd backend
python -m venv venv

# Linux / macOS:
source venv/bin/activate

# Windows:
venv\Scripts\activate

pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

**API docs:** http://localhost:8000/docs

### Frontend

```bash
cd frontend
npm install
npm run dev
```

**Frontend:** http://localhost:3000

## Running With Docker

Make sure Docker and Docker Compose are installed.

From the project root:

```bash
docker compose up --build
```

**Services:**

- **Backend:** http://localhost:8000
- **API docs:** http://localhost:8000/docs
- **Frontend:** http://localhost:3000

To stop:

```bash
docker compose down
```

## Data

Current data: `backend/app/data/wvs_data.json`

Contains mock data for development. Will be replaced with real World Values Survey data in later stages.

## Tech Stack

| Layer    | Technologies                    |
|----------|---------------------------------|
| Backend  | FastAPI, Python, Uvicorn        |
| Frontend | React, TypeScript, Vite, Three.js, Recharts, Zustand |
| DevOps   | Docker, Docker Compose          |

## Notes

- This is an early development version.
- Data structure may change.
- Real data pipeline and AI-based processing will be added in later stages.
