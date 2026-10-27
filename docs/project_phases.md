# Project plan: 3 phases

The assignment (*TY B.Tech Integrated Data Warehouse, OLAP and Data Mining Project: Water Quality Monitoring Analytics*) has 10 mandatory parts (A–J). They are grouped into **three phases** that follow the required integrated lifecycle:

```
Raw WQP data → Profiling → Curation → EDA            (Phase 1)
            → Dimensional DW → Batch / Distributed / Real-time DW → OLAP → Dashboards   (Phase 2)
            → Analytical Mart → Regression / Association Rules / Decision Tree → Architecture evaluation   (Phase 3)
```

Each phase consumes the output of the previous one, so ML never reads the raw file directly (assignment §18).

---

## Phase 1: Data understanding, curation and EDA ✅ *(current)*

| Assignment part | Deliverable | Location |
|---|---|---|
| A. Dataset understanding | Source documentation, data dictionary | `data/README.md`, `docs/data_dictionary.md` |
| A. Data curation (§4) | Data-curation notebook, data-quality report | `notebooks/phase1_curation_eda/01_data_profiling_and_curation.ipynb`, `reports/tables/01_*.csv` |
| A. EDA (§5) | EDA notebook, figures | `notebooks/phase1_curation_eda/02_exploratory_data_analysis.ipynb`, `reports/figures/02_*.png` |
| Business questions Q1–Q8 (§3) | First EDA-level answers | notebook 02 |

**Output handed to Phase 2:** `data/processed/wq_curated.parquet`, which contains every curated row, flags, derived attributes and canonical units.

**Assessment weight:** Business problem 5 + Profiling/Curation/EDA 10 = **15 marks**.

---

## Phase 2: Data warehouse, OLAP and dashboards

| Assignment part | Work |
|---|---|
| B. Dimensional modelling (§6–8) | Grain declaration. Star schema (`FactWaterQualityResult` + DimDate, DimTime, DimMonitoringLocation, DimCharacteristic, DimOrganizationMethod). Snowflake schema. Fact constellation (+ `FactSiteMonthlyWaterQuality`). Comparison and selection |
| Enrichment | Join the **WQP Station** file on `MonitoringLocationIdentifier` → `MonitoringLocationTypeName`, `StateCode`, `HUC` (watershed). Replace the approximate `RegionGroup` |
| C. Batch DW (§9–10) | PostgreSQL staging → dimensions → facts, surrogate keys, FK/RI validation, indexes, **initial load + incremental load of the held-back final month**, reconciliation, timed analytical SQL |
| D. Distributed DW (§11) | Spark → Citus. Distribution-key evaluation (LocationKey / CharacteristicKey / DateKey), skew analysis, centralized vs distributed timings at 100K / 500K / 1M / 2M+ / full |
| E. Real-time DW (§12) | **Simulated** replay ordered by `StartDateTimeUTC`: Python producer → Kafka → Spark Structured Streaming → ClickHouse → Grafana, with the 8 live KPIs |
| F. OLAP + dashboard (§13–14) | Roll-up, drill-down, slice, dice, pivot, CUBE/GROUPING SETS. Power BI (4 pages). 10 numbered findings |

**Assessment weight:** 12 + 10 + 10 + 10 + 8 + 10 = **60 marks**.

---

## Phase 3: Data mining and architecture evaluation

| Assignment part | Work |
|---|---|
| Analytical mart (§18) | `WaterQualityAnalyticsMart` generated **from the warehouse** |
| G. Regression (§15) | Predict DO (or turbidity) from co-measured temperature, pH, conductivity, season, site and previous result. Simple + multiple linear regression (+ Ridge/Lasso/RF). Chronological split, MAE/MSE/RMSE/R², residual and multicollinearity diagnostics |
| H. Association rules (§16) | Apriori (mandatory) + FP-Growth on banded items (Region, ParameterGroup, ResultBand, Season, LocationType, flags). ≥ 10 meaningful rules with support/confidence/lift |
| I. Decision tree (§17) | Classify `HighRiskFlag`. Tune depth/min-samples/ccp-alpha. Accuracy, precision, recall, F1, ROC-AUC, confusion matrix, 3 interpreted decision paths. No target-derived predictors |
| J. Architecture evaluation (§19) | Batch vs distributed vs real-time comparison with measured evidence, plus a hybrid recommendation |

**Assessment weight:** 7 + 6 + 7 + 3 = **23 marks** (+ 2 viva).

---

## Mapping of the 10 mandatory business questions

| # | Question | Phase 1 (EDA) | Final answer in |
|---|---|---|---|
| 1 | Measurements over time | §4 monthly/weekly trends | Phase 2 dashboard |
| 2 | Compare parameters across sites | §5 site-median spread | Phase 2 dashboard |
| 3 | Extreme / poor-quality sites | §5 worst sites, poor-site rule | Phase 2 dashboard + streaming alerts |
| 4 | Seasonal variation | §4.1 seasonal box plots, heat map | Phase 2 OLAP |
| 5 | pH / DO / turbidity / nutrient relationships | §7 site-day correlations | Phase 3 regression |
| 6 | Exceedances / high-risk with thresholds | §6 | Phase 2 + 3 |
| 7 | Site and organization coverage | §5 coverage, site × month heat map | Phase 2 dashboard |
| 8 | Monitoring-location types | §3 proxy (media subdivision) | Phase 2 after Station enrichment |
| 9 | Frequent condition combinations | n/a | Phase 3 association rules |
| 10 | Predict a parameter, classify high risk | n/a | Phase 3 regression + decision tree |
