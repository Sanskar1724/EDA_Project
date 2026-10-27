# Data dictionary

## 1. Source fields used (WQP `resultphyschem.csv`)

Only fields used in the analysis are listed. The full missing-value profile of all 81 source columns is in `reports/tables/01_missing_values_all_columns.csv`.

| Project name | WQP source column | Meaning | Missing in raw |
|---|---|---|---|
| ResultId | ResultIdentifier | Unique result identifier (source key) | 0% |
| OrganizationId / OrganizationName | OrganizationIdentifier / OrganizationFormalName | Submitting organization | 0% |
| ActivityType | ActivityTypeCode | Field measurement, sample, QC, logger … | 0% |
| MediaSubdivision | ActivityMediaSubdivisionName | Surface water, groundwater, ocean … | 78% |
| StartDate / StartTime / TimeZone | ActivityStartDate, ActivityStartTime/Time, …/TimeZoneCode | Sampling date/time | 0% / 29% / 29% |
| EndDate / EndTime | ActivityEndDate, ActivityEndTime/Time | Activity end | 46% / 59% |
| DepthValue / DepthUnit | ActivityDepthHeightMeasure/* | Sampling depth | 77% |
| SiteId / SiteName | MonitoringLocationIdentifier / MonitoringLocationName | Monitoring site | 0% |
| Latitude / Longitude | ActivityLocation/LatitudeMeasure, …/LongitudeMeasure | Activity coordinates | 0.2% |
| CollectionMethod / CollectionEquipment | SampleCollectionMethod/MethodName, SampleCollectionEquipmentName | Sampling method | 63% / 54% |
| Characteristic | CharacteristicName | Measured parameter (6 values) | 0% |
| Speciation | MethodSpeciationName | e.g. nitrate *as N* / *as NO3* | 99% |
| SampleFraction | ResultSampleFractionText | Total, dissolved … | 59% |
| ResultRaw | ResultMeasureValue | Measured value (text) | 0.6% |
| UnitRaw | ResultMeasure/MeasureUnitCode | Reported unit | 14% (pH has no unit) |
| DetectionCondition | ResultDetectionConditionText | Non-detect information | 99.7% |
| QualifierCode | MeasureQualifierCode | Result qualifiers (e.g. SUS = suspect) | 98% |
| ResultStatus | ResultStatusIdentifier | Final, Accepted, Preliminary, Rejected … | 0% |
| ValueType | ResultValueTypeName | Actual / Calculated / Estimated | 0% |
| Provider | ProviderName | STORET (EPA WQX) / NWIS (USGS) | 0% |

**Not in the result file** (needs the WQP *Station* file in Phase 2): `MonitoringLocationTypeName`, `StateCode`, `CountyCode`, `HUCEightDigitCode`.

## 2. Derived attributes (created in notebook 01, *not* source fields)

| Attribute | Rule |
|---|---|
| SampleTypeGroup | ActivityTypeCode → Quality Control / Logger-Continuous / Field Measurement / Lab Sample / Unknown |
| IsProvisional | ResultStatus ∈ {Preliminary, Provisional, Raw, Unreviewed} |
| ParseStatus, ValueNumeric, CensoredLimit | Numeric / `=`-stripped / censored `<x` / text placeholder / missing |
| ValueStd, UnitStd, UnitStatus | Value in canonical unit (°C, mg/L, pH units, NTU, µS/cm, mg/L as N) |
| IsImpossible | Outside the physical range (Temp −5–50, DO 0–30, pH 0–14, Turb 0–10,000, Cond 0–100,000, NO₃ 0–100) |
| TimeKnown, Hour | Valid time that is not a midnight placeholder on a discrete visit |
| StartDateTimeLocal, StartDateTimeUTC | Local timestamp and UTC (zone-code offset) |
| CoordinateValid, RegionGroup | Approximate longitudinal bands (West, Mountain/Plains, Central, East, Alaska, Hawaii/Pacific, Caribbean, Unknown) |
| IsLogicalDuplicate, HasConflictingValue | Same site, datetime, parameter, fraction, depth, activity type (± same value) |
| ExclusionReason, IsAnalysisReady | First matching exclusion rule, or *Included* |
| IsExtremeOutlier | Beyond Q1 − 3·IQR or Q3 + 3·IQR (per parameter), retained |
| Year, Quarter, Month, YearMonth, WeekStart, DayOfWeek | Calendar decomposition of StartDate |
| Season | DJF Winter, MAM Spring, JJA Summer, SON Fall |
| TimeBand | Night 00–05, Morning 06–11, Afternoon 12–17, Evening 18–23, Unknown |
| ParameterGroup | Physical (Temp, Turbidity, Conductivity) · Chemical (pH) · Oxygen (DO) · Nutrient (Nitrate) |
| ResultBand | Ordered parameter-specific bands (see below) |
| ExceedanceFlag, HighRiskFlag | 0/1 per thresholds below (analysis-ready rows only) |
| SiteDailyAverage | Mean ValueStd per Site × Date × Characteristic |
| LocationType | Placeholder "Not Available" until Phase 2 Station enrichment |

## 3. Thresholds

| Parameter | ResultBand cut-points | Exceedance | High risk | Source |
|---|---|---|---|---|
| pH | 6.5, 9.0 | < 6.5 or > 9.0 | < 6.0 or > 9.5 | EPA aquatic-life criterion 6.5–9.0. High-risk band is project-defined |
| DO (mg/L) | 3, 5, 8 | < 5 | < 3 | EPA 1986 *Quality Criteria for Water*. < 3 is project-defined |
| Nitrate (mg/L N) | 1, 3, 10 | > 10 | > 20 | EPA drinking-water MCL (10 mg/L N). 2×MCL is project-defined |
| Temperature (°C) | 10, 20, 28 | > 28 | > 32 | project-defined (typical warm-water standards) |
| Turbidity (NTU) | 5, 25, 100 | > 25 | > 100 | project-defined (common state limit) |
| Conductivity (µS/cm) | 150, 500, 1500 | > 1500 | > 3000 | project-defined. Not applied to Ocean Water |
