# GloFAS Seasonal (Global Flood Awareness System — seasonal forecast)

## What this model is
GloFAS Seasonal is the long-range configuration of the Copernicus Emergency Management Service global river discharge forecast. It runs the same LISFLOOD hydrological model on the same 0.05° global grid as [GloFAS Forecast](./cems-glofas-forecast.md), but is forced with ECMWF's SEAS5 seasonal ensemble instead of medium-range meteorology, initialised once a month and extending to 215 days.

It forecasts **discharge**, not water level.

> **This entry is filed under `hydrological_models/` rather than `climate_models/`**, following the repository's convention that seasonal forecasts of a phenomenon use the phenomenon's template and sit beside their medium-range siblings. It uses `hydrological-model.template.md` with the optional **Long-range configuration** section retained, and points upstream to [SEAS5](../../../climate_models/global/eu/seas5.md) rather than duplicating that system's coupled configuration. Cross-linked in both directions with [GloFAS Forecast](./cems-glofas-forecast.md).

---

## Who runs it
- **Organization:** Copernicus Emergency Management Service (CEMS) — European Commission Joint Research Centre (JRC), implemented by ECMWF
- **Country / region:** International (European Union programme)

---

## What area it covers
- **Coverage:** Global except Antarctica
- **Domain details:** 90°N–60°S, 180°W–180°E, regular latitude–longitude grid on EPSG:4326 — identical to the medium-range product
- **Grid dimensions (computed, not file-verified):** 7200 × 3000 at 0.05°

---

## Basic details
- **Model type:** Ensemble hydrological model (seasonal range)
- **Core hydrological model:** LISFLOOD (open source; JRC)
- **Forecast length:** 5160 h (215 days), 215 lead-time steps at 24-hour intervals — verified against the EWDS request constraints
- **Update frequency / cycles:** monthly, initialised on the 1st at 00 UTC
- **Publication:** made available on the 10th of each month
- **Temporal output resolution:** 24 h
- **Operational system version:** GloFAS v4.5 (16 April 2026). Legacy versions `version_2_2` and `version_3_1` remain retrievable.

---

## Grid and river network
- **Land surface grid:** 0.05° (~5 km at the equator). Raised from 0.1° at v4.0.
- **Routing geometry:** Gridded routing on the same 0.05° grid; no reach-indexed channel product
- **Calibration:** shared with the operational GloFAS chain — 1995 in-situ gauging stations with parameter regionalisation for ungauged catchments (v4.0)
- **Reservoir and lake representation:** TBD

---

## Meteorological forcing
- **Driving atmospheric model:** [SEAS5](../../../climate_models/global/eu/seas5.md) — ECMWF's seasonal forecasting system, 51 members at ~36 km, initialised monthly from the 1st with 7-month lead time (extended to 13 months once a season)
- **Forcing quantity:** **downscaled runoff forecasts** from SEAS5, rather than downscaled precipitation and temperature. This is a meaningful difference from the medium-range chain and from EFAS Seasonal, and it means part of the land surface response is inherited from the SEAS5 land model rather than computed by LISFLOOD. (TBD — the downscaling method is not described on the public pages.)
- **Bias correction:** TBD

---

## Initialization and antecedent state
- **Initial state source:** the operational GloFAS chain's continuous simulation at the start of the month (TBD — not documented in detail for the seasonal configuration specifically)
- **Assimilates discharge observations:** TBD
- **No-DA variant published?** No

---

## Ensemble configuration
- **Ensemble size:** 51
- **Source of perturbations:** inherited entirely from SEAS5. The hydrological model is not perturbed.
- **Member packaging:** unlike the medium-range dataset, the request form exposes **no `product_type` selector** — control and perturbed members are not separable at request time
- **Derived products distributed:** none as raw data. The monthly discharge anomaly categories shown on GloFAS-IS are viewer products, not downloadable grids — out of scope.

---

## Long-range configuration
- **Driving long-range system:** [SEAS5](../../../climate_models/global/eu/seas5.md)
- **Initialization cadence:** 1st of each month
- **Hindcast (reforecast) period:** 1981–2016 with 25 members, 2017 onward with 51 members, following SEAS5's own reforecast configuration
- **Reference climatology period:** 1979-01-01 to 2022-12-31 for thresholds and anomalies (GloFAS v4 reference climatology)
- **Hindcasts distributed alongside forecasts?** No — separate dataset, `cems-glofas-seasonal-reforecast` on EWDS. The seasonal reforecasts are used to derive a **forecast-range-dependent** climatology, so anomaly products are not computed against a single fixed baseline.
- **Sources of predictability:** TBD — not documented on the public pages. Note the general point that seasonal hydrological skill often leans more on initial catchment storage than on atmospheric predictability, which the range-dependent climatology design implicitly acknowledges.

---

## What it provides
- **River discharge in the last 24 hours** (m³ s⁻¹, surface level)
- **Soil wetness index (root zone)** (sub-surface)
- **Snow depth water equivalent** (surface)
- **Runoff water equivalent (surface plus subsurface)** (sub-surface)

Time-invariant ancillary files, required for interpretation and version-matched to the forecast:
- **Upstream area** (m² per river pixel)
- **Elevation**

---

## Data availability
- **Is the data free?** Yes, with free self-registration on the CEMS Early Warning Data Store
- **License:** **CEMS-FLOODS datasets licence** — free reproduction, distribution, communication to the public, and adaptation. Attribution required: *"Generated using Copernicus Emergency Management Service information [Year]"*, or *"Contains modified Copernicus Emergency Management Service information [Year]"* for adapted data. No share-alike or non-commercial restriction. The EWDS catalogue API reports `license: other`; the full text is linked below.
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded fields only
- **Real-time availability:** **Current, no embargo.** Catalogue temporal extent ran to 2026-08-01 when checked on 2026-08-11, consistent with monthly initialisation and the documented 10th-of-month publication. `cads:update_frequency` is `Monthly`.
- **Data formats:** GRIB2, and NetCDF-4 offered in the request form
  - **Note — format documentation conflict.** The CEMS Model Output page lists File format as **GRIB2 only** for this dataset, while the live EWDS request form offers both `grib2` and `netcdf`. The form is the operative source; the documentation is stale.
- **Official download location:**
  https://ewds.climate.copernicus.eu/datasets/cems-glofas-seasonal
- **DOI:** https://doi.org/10.24381/cds.00b6c4fb
- **Archive extent:** 2020-12-01 to present
- **Access route notes:** API request against the EWDS processes endpoint with an account key, or the web form. No anonymous directory tree.

> **Verification status.** Verified live against the EWDS catalogue, request form, constraints and licence endpoints, plus ECMWF CEMS documentation. **No GRIB or NetCDF file was decoded** — EWDS retrieval requires an authenticated account key — so grid dimensions, GRIB parameter encoding, packing and bitmap coverage are documentation-derived and unconfirmed.

---

## Notes

- **Documentation error — forecast length.** The EWDS dataset description states the forecasts "cover 123 days". They do not. The live request constraints expose **215 lead-time steps to 5160 h**, and the CEMS Model Output page resolves the contradiction: the forecasts covered 123 days until May 2021 and **215 days from June 2021 onward**. The dataset description on the data store has not been updated in five years. Anyone sizing a request from the description will under-fetch by 92 days.

- **Documentation error — resolution.** The CEMS Model Output page's prose for this product states LISFLOOD is forced "at a 0.1° (~11 km at the equator) resolution", while its own technical table two lines below states 0.05°. The prose is stale text predating v4.0 (July 2023). The correct figure is 0.05°.

- **The EWDS catalogue bounding box is wrong.** The STAC record reports `bbox: [0.0, -60.0, 360.0, 90.0]`, which is a 0–360 longitude convention against a documented −180/+180 grid. The medium-range record carries a different and equally implausible box. Do not use catalogue geometry for domain statements.

- **Forced with runoff, not with weather.** Worth restating because it is unusual: GloFAS Seasonal takes **downscaled runoff** from SEAS5, whereas the medium-range chain takes meteorological fields. The land surface partitioning is therefore partly SEAS5's rather than LISFLOOD's, which matters for anyone comparing the seasonal and medium-range products as though they were the same model at different ranges.

- **Discharge accumulation semantics.** *River discharge in the last 24 hours* is a daily mean over the preceding window, not an instantaneous value at the timestamp.

- **No control/perturbed split.** Unlike the medium-range dataset, this one has no `product_type` selector in the request form. If the control member is needed separately, it must be identified from the ensemble dimension after retrieval.

- **Relationship to other CEMS-Flood datasets.** Medium-range sibling: [GloFAS Forecast](./cems-glofas-forecast.md). European counterpart at 1 arcminute: [EFAS Seasonal](../../regional/eu/efas-seasonal.md). The seasonal hindcast archive (`cems-glofas-seasonal-reforecast`) and the ERA5-forced historical simulation (`cems-glofas-historical`) are historical-only and out of scope as standalone entries.

- **AI relationship.** The AIFS Single forcing introduced at GloFAS v4.4 applies to the medium-range chain out to 15 days. This seasonal product is forced by SEAS5 and is **not** AI-influenced on current evidence. If that changes, update [`AI_MODELS.md`](../../../../AI_MODELS.md).

- **Not a flood warning.** The licence states that CEMS-Flood data does not constitute a flood warning, which only national and regional institutions are authorised to issue.

---

## Recent version history
Shares the GloFAS operational versioning chain — see [GloFAS Forecast](./cems-glofas-forecast.md#recent-version-history) for the full sequence. Points specific to the seasonal product:

- **v4.3 — 4 June 2025:** CEMS-Flood long-range product design harmonised across EFAS and GloFAS (EFAS from v5.4, 12 March).
- **v4.0 — 26 July 2023 (major):** seasonal forecasts updated to the newly calibrated higher-resolution hydrological model; resolution 0.1° → 0.05°.
- **June 2021:** forecast length extended from 123 to 215 days.

Full chronology: https://confluence.ecmwf.int/display/CEMS/GloFAS+versioning+system

---

## Official documentation
- Dataset page: https://ewds.climate.copernicus.eu/datasets/cems-glofas-seasonal
- GloFAS wiki: https://confluence.ecmwf.int/display/CEMS/Global+Flood+Awareness+System
- Model output specifications: https://confluence.ecmwf.int/display/CEMS/Model+Output
- Meteorological forcing: https://confluence.ecmwf.int/display/CEMS/GloFAS+meteorological+forecasts
- Sub-seasonal and seasonal forecasting: https://confluence.ecmwf.int/display/CEMS/CEMS-Flood+sub-seasonal+and+seasonal+forecasting
- Seasonal forecast skill: https://confluence.ecmwf.int/display/CEMS/GloFAS+seasonal+forecast+skill
- Versioning system: https://confluence.ecmwf.int/display/CEMS/GloFAS+versioning+system
- Auxiliary data guide: https://confluence.ecmwf.int/display/CEMS/Auxiliary+Data
- Web portal: https://www.globalfloods.eu/
- Licence text: https://object-store.os-api.cci2.ecmwf.int/cci2-prod-catalogue/licences/cems-floods/cems-floodsv1_34fd7d51830f6470b596eb16b6cf9a4e36605fe3acf3c2406e021591499e8c32.md
