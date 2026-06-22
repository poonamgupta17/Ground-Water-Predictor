# Groud-Water-Predictor Dashboard

A client-side groundwater analysis and forecasting dashboard built with vanilla HTML, CSS, and JavaScript. It ingests historical station data from a local CSV, runs linear regression to predict future water levels, and visualises everything across an interactive card grid and a clustered map of India.

---

## ✨ Features

- **Station Cards** — Each monitoring station gets its own card showing the most recent recorded level alongside a linear-regression forecast
- **Multi-horizon Predictions** — Toggle between Week / Month / Year forecasts per station on the fly
- **Critical Alert System** — Cards automatically surface a warning banner when the predicted level is projected to exceed the critical threshold (30m)
- **Historical Data Modal** — Inspect the full time-series table for any station without leaving the page
- **Interactive Map View** — All stations plotted on a Leaflet.js map with marker clustering; click any marker for a quick summary popup
- **Live Search** — Debounced search filters stations by name or district in real time (triggers after 3 characters)
- **Deep Ocean UI** — Dark-mode-first design using a custom CSS variable palette (`#0d1b2a` base, `#3a86ff` accent)
- **Zero backend** — Entirely static; runs from the file system or any basic HTTP server

---

## 🗂️ Project Structure

```
hydro-analytics/
├── data/
│   └── master_data.csv   # Station records (station_name, date, currentlevel, latitude, longitude, …)
|   |__ historical_data.csv
├── index.html            # App shell and markup
├── script.js             # Data loading, parsing, prediction logic, and all event handling
└── style.css             # Deep Ocean theme and component styles
```

---

## 📐 How the Prediction Works

The forecast uses **Ordinary Least Squares linear regression** computed entirely in the browser:

1. Each station's historical records are converted into a `(days_since_first_record, water_level)` time series.
2. The slope and intercept are derived from the standard OLS formulae.
3. The predicted level at `last_known_day + N` is returned for N = 7, 30, or 365.

> This is a trend-based approximation. It works best for stations with a consistent long-term trajectory and should not be used as a substitute for hydrological modelling.

---

## 📋 CSV Format

Your `data/master_data.csv` must include at least these columns:

| Column | Description |
|---|---|
| `station_name` | Unique name for the monitoring station |
| `district_name` | District the station belongs to |
| `state_name` | State the station belongs to |
| `date` | Observation date in `YYYY-MM-DD` format |
| `currentlevel` | Water level reading in metres |
| `latitude` | Decimal latitude for map plotting |
| `longitude` | Decimal longitude for map plotting |

---

## 🛠️ Running Locally

The app uses `Papa.parse` with `download: true`, which requires a server context (browsers block local file XHR by default).

**Option 1 — VS Code Live Server**
Install the Live Server extension, right-click `index.html`, and select *Open with Live Server*.

**Option 2 — Python**
```bash
python -m http.server 8080
# then open http://localhost:8080
```

**Option 3 — Node.js**
```bash
npx serve .
```

---

## 🧰 Dependencies (all via CDN, no install needed)

| Library | Purpose |
|---|---|
| [Leaflet 1.9.4](https://leafletjs.com/) | Interactive map |
| [Leaflet.markercluster 1.4.1](https://github.com/Leaflet/Leaflet.markercluster) | Station marker clustering |
| [PapaParse 5.3.0](https://www.papaparse.com/) | CSV parsing |
| [Font Awesome 6.5.2](https://fontawesome.com/) | Icons |
| [Inter (Google Fonts)](https://fonts.google.com/specimen/Inter) | Typography |

---

## 🚧 Known Limitations

- Predictions are linear; seasonal or cyclical groundwater patterns will reduce accuracy
- The critical alert threshold (30m) is currently hardcoded in `script.js`
- No data validation UI — malformed CSV rows are silently dropped
