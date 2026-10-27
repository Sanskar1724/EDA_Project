# Phase 2: Data Warehouse, OLAP and Dashboards

Planned notebooks/scripts (see `docs/project_phases.md`):

- `03_dimensional_modelling.ipynb`: grain, star, snowflake, fact constellation
- `04_station_enrichment.ipynb`: WQP Station join (LocationType, State, HUC)
- `05_batch_dw_postgres.ipynb`: staging, dimensions, facts, incremental load
- `06_distributed_dw_citus.ipynb`: distribution keys, skew, performance experiment
- `07_realtime_simulation/`: Kafka producer, Spark streaming, ClickHouse, Grafana
- `08_olap_cube.ipynb`: roll-up, drill-down, slice, dice, pivot, CUBE

Input: `data/processed/wq_curated.parquet` from Phase 1.
