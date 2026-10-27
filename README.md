# Water Quality Monitoring Analytics
### Batch, Distributed & Real-Time Data Warehouse with OLAP, Dashboards and Data Mining

TY B.Tech Data Warehousing project built on the **Water Quality Portal (WQP)** dataset (USGS + U.S. EPA + National Water Quality Monitoring Council): **4.96 million** physical/chemical results from **50,476** monitoring sites and **486** organizations, covering 2024–2025.

Parameters: **water temperature, dissolved oxygen, pH, turbidity, conductivity, nitrate**.

---

## Project phases

| Phase | Scope | Status |
|---|---|---|
| **1. Data understanding, curation & EDA** | Profiling, data-quality report, unit standardisation, derived attributes, EDA (numerical, categorical, temporal, geographic, outcome) | ✅ done |
| **2. Data warehouse, OLAP & dashboards** | Star / snowflake / fact-constellation design, PostgreSQL batch DW with incremental load, Citus distributed DW, Kafka → Spark → ClickHouse real-time simulation, OLAP cube, Power BI + Grafana | ⏳ next |
| **3. Data mining & evaluation** | Analytical mart, regression, Apriori / FP-Growth, decision tree, architecture comparison and recommendation | ⏳ |

Details and question-to-phase mapping: [docs/project_phases.md](docs/project_phases.md)

---

## Repository structure

```
├── data/
│   ├── README.md                 # dataset source, citation, where to put the CSV
│   ├── raw/                      # resultphyschem.csv (not committed, 2.5 GB)
│   └── processed/                # wq_curated.parquet (generated, not committed)
├── docs/
│   ├── project_phases.md         # 3-phase plan mapped to assignment parts A–J
│   └── data_dictionary.md        # source fields, derived attributes, thresholds
├── notebooks/
│   ├── phase1_curation_eda/
│   │   ├── 01_data_profiling_and_curation.ipynb
│   │   └── 02_exploratory_data_analysis.ipynb
│   ├── phase2_data_warehouse/    # coming next
│   └── phase3_data_mining/
├── reports/
│   ├── figures/                  # charts exported by the notebooks
│   └── tables/                   # data-quality report and EDA tables (CSV)
├── requirements.txt
└── README.md
```

---

## How to run

### Option A: Kaggle (recommended, 30 GB RAM)
1. Add the WQP dataset (containing `resultphyschem.csv`) as input to a new notebook.
2. Import `notebooks/phase1_curation_eda/01_data_profiling_and_curation.ipynb` and **Run All**. It finds the CSV under `/kaggle/input/` automatically and writes `/kaggle/working/data/processed/wq_curated.parquet`.
3. **Save Version**. Then open `02_exploratory_data_analysis.ipynb`, choose *Add Input → Your Work →* the notebook from step 2, and **Run All**.

### Option B: Local
```bash
pip install -r requirements.txt
# put the file at data/raw/resultphyschem.csv (or resultphyschem/resultphyschem.csv)
jupyter notebook notebooks/phase1_curation_eda/
```
The full file needs about 8 GB+ of free RAM. For a quick test, set `SAMPLE_NROWS = 300_000` in the first code cell of notebook 01.

---

## Phase 1 highlights
* **Profiling before cleaning:** missing values for all 81 columns, exact duplicates (whole-row hash) and source-key uniqueness.
* **Nothing dropped silently:** each row gets one `ExclusionReason`, and the data-quality report lists the attribute, count, treatment and justification for every decision ([reports/tables/01_data_quality_report.csv](reports/tables/01_data_quality_report.csv)).
* **Unit standardisation:** °F→°C, mS/cm→µS/cm, nitrate as NO₃→as N, with DO % saturation separated from mg/L.
* **Documented thresholds** for exceedance and high-risk flags (EPA criteria where they exist, clearly labelled project thresholds otherwise).
* **Row reconciliation:** raw = duplicates removed + curated, and curated = analysis-ready + Σ excluded.

## Dataset citation
Water Quality Portal. Washington (DC): National Water Quality Monitoring Council, United States Geological Survey (USGS), Environmental Protection Agency (EPA); 2021. https://doi.org/10.5066/P9QRKUVJ. Data downloaded via https://www.epa.gov/waterdata/water-quality-data-download.
