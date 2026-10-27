# Data

The raw data is **not committed** to Git because it is 2.5 GB. Download it as described below.

## Source

| Item | Value |
|---|---|
| Dataset | Water Quality Portal (WQP): *Sample Results (physical/chemical metadata)* (`resultphyschem.csv`) |
| Publisher | USGS + U.S. EPA + National Water Quality Monitoring Council |
| Official link | https://www.epa.gov/waterdata/water-quality-data-download (portal: https://www.waterqualitydata.us) |
| Characteristics | Temperature, water · Dissolved oxygen (DO) · pH · Turbidity · Conductivity · Nitrate |
| Period | 2024-01-01 to 2025 (two years) |
| Media | Water |
| Rows | 4,958,238 results · 81 columns · 50,476 monitoring locations · 486 organisations |
| File size | ~2.5 GB (CSV) |
| Providers | STORET (EPA WQX) and NWIS (USGS) |
| Extraction date | *fill in: date you downloaded the file from WQP* |
| Query URL | *fill in: the WQP query URL used for the download (keep it for reproducibility)* |

## Where to put the file

| Environment | Location |
|---|---|
| Local | `data/raw/resultphyschem.csv` **or** `resultphyschem/resultphyschem.csv` |
| Kaggle | Add the Kaggle dataset as notebook input. The notebook searches `/kaggle/input/**/resultphyschem*.csv` automatically |

## Generated files (also not committed)

| File | Produced by | Used by |
|---|---|---|
| `data/processed/wq_curated.parquet` | `01_data_profiling_and_curation.ipynb` | `02_exploratory_data_analysis.ipynb`, Phase 2 ETL |
