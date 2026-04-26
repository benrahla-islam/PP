# 🇩🇿 Algeria Green Hydrogen LCOH Grid

Spatial **Levelized Cost of Hydrogen (LCOH)** mapping for Algeria using:

- **ERA5 weather data** (solar + wind resource)
- **atlite** for capacity-factor extraction
- **PyPSA** for techno-economic optimization
- **Streamlit + Plotly** for interactive visualization

---

## What this project does

This project computes LCOH per grid cell across Algeria and visualizes the results on an interactive map.

Pipeline:

1. Build grid cells over Algeria
2. Merge ERA5 files and extract hourly solar/wind CF per cell
3. Solve a PyPSA model for each cell
4. Save costs + capacities + performance metrics to CSV
5. Explore the results in a Streamlit dashboard

---

## Project structure

- [atlite_grid.py](atlite_grid.py): ERA5 merge + CF extraction (default 1°)
- [atlite_grid_2deg.py](atlite_grid_2deg.py): same workflow at 2° default
- [pypsa_model.py](pypsa_model.py): PyPSA network definition + result extraction
- [pypsa_grid.py](pypsa_grid.py): run PyPSA for all cells, write final grid results
- [app_grid.py](app_grid.py): Streamlit app for map/statistics
- [utils.py](utils.py): financial parameters + helper math
- [data/](data): input/output datasets

---

## Requirements

- Python **3.12+**
- Linux/macOS/Windows
- ERA5 NetCDF files in [data/](data)

Install dependencies (pick one):

### Option A — using `uv` (recommended)

```bash
uv sync
```

### Option B — using venv + pip

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

---

## Input data required

Place these files in [data/](data):

- `era5_p1_accum.nc`
- `era5_p1_instant.nc`
- `era5_p2_accum.nc`
- `era5_p2_instant.nc`
- `era5_p3_accum.nc`
- `era5_p3_instant.nc`

---

## How to run

### 1) Extract weather capacity factors

```bash
python atlite_grid.py --year 2024 --resolution 1.0
```

Outputs:

- `data/grid_cells.csv`
- `data/solar_cf_grid.csv`
- `data/wind_cf_grid.csv`
- `data/grid_cf_stats.csv`

### 2) Run PyPSA across all cells

```bash
python pypsa_grid.py
```

Useful options:

```bash
python pypsa_grid.py --weeks 8
python pypsa_grid.py --h2-demand 5000
python pypsa_grid.py --no-wind
python pypsa_grid.py --no-grid
python pypsa_grid.py --resume
```

Main output:

- `data/lcoh_grid.csv`

### 3) Launch dashboard

```bash
streamlit run app_grid.py
```

If using `uv`:

```bash
uv run streamlit run app_grid.py
```

---

## Dashboard features

- LCOH map (USD/kg and DZD/kg)
- Solar and wind resource maps
- Statistical plots and top-cell rankings
- Financial/technology scenario sliders
- CSV export

---

## Notes

- `lcoh_grid.csv` is the core app input.
- If the app shows no data, first regenerate with:
	1. `python atlite_grid.py`
	2. `python pypsa_grid.py`
- Streamlit command requires `streamlit run ...` (not `streamlit app_grid.py`).

---

## Typical workflow

```bash
python atlite_grid.py --year 2024 --resolution 1.0
python pypsa_grid.py --resume
streamlit run app_grid.py
```

---

## License

Internal/research use unless a dedicated license file is added.

