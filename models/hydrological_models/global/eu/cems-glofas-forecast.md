# GloFAS Forecast (Global Flood Awareness System — 30-day forecast)

## What this model is
GloFAS Forecast is the global river discharge forecast of the Copernicus Emergency Management Service. It forecasts **discharge**, not water level — the LISFLOOD hydrological model generates runoff from a land surface and routes it through a global river network, producing daily mean discharge in m³ s⁻¹ on a 0.05° grid together with the land surface states that produced it.

The dataset distributed through the CEMS Early Warning Data Store is the **30-day configuration**: IFS ENS meteorology for the first 15 days spliced onto one-day-old extended-range meteorology for days 16–30, run as 51 members. This is not the same as the forecast now shown on the GloFAS web portal, which moved to a 46-day sub-seasonal configuration at v4.3 — see *Notes*.

GloFAS is managed, technically implemented and developed by the European Commission's Joint Research Centre, with ECMWF operating the forecast chain.

---

## Who runs it
- **Organization:** Copernicus Emergency Management Service (CEMS) — European Commission Joint Research Centre (JRC), implemented by ECMWF
- **Country / region:** International (European Union programme)

---

## What area it covers
- **Coverage:** Global except Antarctica
- **Domain details:** 90°N–60°S, 180°W–180°E, regular latitude–longitude grid on EPSG:4326
- **Grid dimensions (computed, not file-verified):** 7200 × 3000 at 0.05° — derived from the documented bounds and resolution rather than read from a file header. See the verification note at the end of *Data availability*.

---

## Basic details
- **Model type:** Ensemble hydrological model
- **Core hydrological model:** LISFLOOD (open source; JRC)
- **Routing scheme:** LISFLOOD channel routing (TBD — the specific kinematic/dynamic formulation and reservoir handling are not stated on the public version pages)
- **Forecast length:** 720 h (30 days), 30 lead-time steps at 24-hour intervals — verified against the EWDS request constraints
- **Update frequency / cycles:** 1× daily, 00 UTC
- **Temporal output resolution:** 24 h (daily mean; discharge is published as "river discharge in the last 24 hours")
- **Operational system version:** GloFAS v4.5, released 16 April 2026. v4.5 is a minor release affecting the map viewer and reporting point list only, with **no impact on model results**; the modelling chain is unchanged from v4.0.

---

## Grid and river network
- **Land surface grid:** 0.05° (~5 km at the equator), EPSG:4326. Raised from 0.1° at v4.0 (26 July 2023).
- **Routing geometry:** Gridded routing on the same 0.05° grid — GloFAS publishes discharge per grid cell, not per vector reach. Unlike some hydrological systems in this category there is no reach-indexed channel product.
- **Hydrography / DEM source:** New 0.05° input map set introduced at v4.0 from remote sensing and in-situ datasets (TBD — the underlying hydrography product is not named on the version page)
- **Calibration:** 1995 in-situ gauging stations, with parameter regionalisation for ungauged catchments (v4.0)
- **Reservoir and lake representation:** TBD — not documented on the public version pages

---

## Meteorological forcing
- **Driving atmospheric model:** ECMWF IFS ENS (51 members at ~9 km) for lead days 1–15, spliced onto ECMWF extended-range IFS SUBS (~36 km) for days 16–30. Members are paired one-to-one across the splice: ENS member *n* is followed by SUBS member *n*.
- **Splice detail:** the 30-day configuration uses **one-day-old** extended-range meteorology for days 16–30, because the sub-seasonal run is not available early enough to produce a same-morning 30-day forecast. This is the substantive difference from the newer 46-day configuration, which uses same-day sub-seasonal data.
- **Discontinuity at day 15:** the resolution change from 9 km to 36 km across the splice can create local discontinuities, most noticeably in complex orography. ECMWF states the ensemble is not expected to suffer major disruption on average.
- **AI forcing (from v4.4, 10 September 2025):** meteorological fields from **AIFS Single**, ECMWF's deterministic machine-learning forecast at ~28 km, were introduced to force GloFAS out to 15-day lead time. **TBD — whether this supplements or replaces the IFS ENS forcing for the distributed ensemble product is not clear from the documentation**, which describes both as forcing the medium range on the same page. Resolve before relying on the forcing description above for v4.4-and-later cycles. See *Notes* on `AI_MODELS.md`.
- **Bias correction / downscaling:** TBD

---

## Initialization and antecedent state
- **Initial state source:** continuous LISFLOOD run; the "fillup" component of model initialisation specifically uses the IFS control member (IFS CF) rather than the perturbed ensemble, since the control starts from the unperturbed analysis
- **Assimilates discharge observations:** TBD — GloFAS is calibrated against 1995 gauges, but whether real-time gauge data is assimilated into the operational initial state is not stated on the public pages
- **No-DA variant published?** No

---

## Ensemble configuration
- **Ensemble size:** 51 (1 control + 50 perturbed)
- **Source of perturbations:** inherited entirely from the driving ECMWF ensembles — IFS ENS in the first 15 days, IFS SUBS thereafter. The hydrological model itself is not perturbed.
- **Member packaging:** the EWDS request form exposes `control_forecast` and `ensemble_perturbed_forecasts` as separate product types, so the control is retrieved independently of the perturbed set
- **Derived products distributed:** none as raw data through EWDS. Return-period exceedance probabilities and flood signals exist as GloFAS-IS viewer products, not as downloadable grids — out of scope.

---

## What it provides
- **River discharge in the last 24 hours** (m³ s⁻¹, surface level)
- **Soil wetness index (root zone)** (sub-surface)
- **Snow depth water equivalent** (surface)
- **Runoff water equivalent (surface plus subsurface)** (sub-surface)

Two time-invariant ancillary files are distributed separately and are required for meaningful interpretation of the discharge field:
- **Upstream area** — total contributing catchment area per river pixel, in m²
- **Elevation**

Auxiliary data is version-specific. Match the auxiliary file version to the version of the forecast being used, since the upstream area map is tied to the hydrological model configuration and changes between cycles.

---

## Data availability
- **Is the data free?** Yes, with free self-registration on the CEMS Early Warning Data Store
- **License:** **CEMS-FLOODS datasets licence.** Grants free access for reproduction, distribution, communication to the public, and adaptation or modification. Attribution required: *"Generated using Copernicus Emergency Management Service information [Year]"*, or *"Contains modified Copernicus Emergency Management Service information [Year]"* for adapted data. No share-alike or non-commercial restriction. Not an SPDX-identified licence — the EWDS catalogue API reports `license: other`, which is why it needs stating in full here.
  - **Note — restricted-data clause.** The licence reserves that some CEMS EFAS and GloFAS data is restricted under Article 12 of Commission Delegated Regulation (EU) No 1159/2013, with access granted only to specific registered users. This clause is what underpins the EFAS partner restriction; it does **not** bite on this dataset, which is open and current.
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded fields only
- **Real-time availability:** **Current, no embargo.** The catalogue's temporal extent ran to the present day when checked on 2026-08-11, and `cads:update_frequency` is `Daily`. This is the point of difference from [EFAS Forecast](#), whose public feed runs 30 days behind — see *Notes*.
- **Data formats:** GRIB2, and NetCDF-4 flagged **experimental** in the request form
- **Official download location:**
  https://ewds.climate.copernicus.eu/datasets/cems-glofas-forecast
- **DOI:** https://doi.org/10.24381/cds.ff1aef77
- **Archive extent:** 2019-11-05 to present
- **Legacy system versions retrievable:** `version_2_1` and `version_3_1` alongside `operational`. Legacy versions use the `htessel_lisflood` model chain; the operational chain is `lisflood`.
- **Access route notes:** retrieval is by API request against the EWDS processes endpoint with an account key, or through the web form. There is no anonymous directory tree.

> **Verification status.** Everything above marked as verified comes from live queries against the EWDS catalogue, request form and constraints endpoints, and from ECMWF's CEMS documentation. **No GRIB or NetCDF file was decoded**, because EWDS retrieval requires an authenticated account key. Grid dimensions, GRIB parameter numbers, packing, bitmap coverage, and actual publication latency are therefore documentation-derived and unconfirmed. Worth a decode pass once an account is available.

---

## Notes

- **This dataset is the 30-day configuration, which the GloFAS web portal no longer uses.** Since GloFAS v4.3 (4 June 2025) the operational web products run a 46-day sub-seasonal configuration forced with same-day extended-range meteorology, while the medium-range web products cover only the first 15 days. ECMWF states explicitly that users are given *continued* access to the 30-day forecasts through EWDS. So the downloadable product and the portal product have diverged, and the EWDS lead-time ceiling of 720 h confirms the distributed dataset is still the 30-day chain.

- **Documentation error — resolution.** The CEMS Model Output page's prose for the seasonal product states LISFLOOD is forced "at a 0.1° (~11 km at the equator) resolution", while the technical table immediately below it states 0.05°. The 0.1° figure is stale text predating v4.0. The same page's GloFAS Forecast section is correct at 0.05°.

- **Documentation error — release date.** The GloFAS versioning table gives v4.5 as released 2025-04-16, while the latest-release page states it was launched on 16 April **2026**. The 2026 date is consistent with the surrounding sequence (v4.3 June 2025, v4.4 September 2025), so the versioning table year appears to be a typo.

- **The EWDS catalogue bounding box is wrong.** The STAC record reports `bbox: [0.0, -70.0, 360.0, 70.0]`, which is neither the documented domain (90°N–60°S) nor a plausible global extent. Do not use catalogue geometry for domain statements; the same default-looking box appears on the EFAS records, where it is even less applicable.

- **Discharge accumulation semantics.** The variable is *river discharge in the last 24 hours* — a daily mean over the preceding 24-hour window, not an instantaneous value at the timestamp. A file at lead time 24 h covers the first forecast day, not a snapshot at hour 24.

- **Relationship to other CEMS-Flood datasets.** Companion products on EWDS: `cems-glofas-historical` (ERA5-forced reanalysis, used to derive the climatology), `cems-glofas-reforecast` (medium-range hindcasts), and `cems-glofas-seasonal-reforecast`. All three are historical-only and out of scope as standalone entries. The seasonal sibling is [GloFAS Seasonal](./cems-glofas-seasonal.md). The European counterpart at higher resolution is [EFAS Seasonal](../../regional/eu/efas-seasonal.md); the EFAS medium-range forecast is **not** catalogued, because its public feed is embargoed by 30 days with real-time access restricted to EFAS partners.

- **AI relationship.** From v4.4, AIFS Single meteorology forces GloFAS out to 15 days, making this a downstream AI-influenced hydrological product in the same way GIOPS is a downstream AI-influenced ocean product via the GEML-nudged GDPS atmosphere. [`AI_MODELS.md`](../../../../AI_MODELS.md) should index this once the supplement-versus-replace question above is resolved. **Flagged for decision rather than resolved here.**

- **Not a flood warning.** The licence states plainly that GloFAS data does not constitute a flood warning, which only national and regional institutions are authorised to issue within their areas of responsibility.

---

## Recent version history
- **v4.5 — 16 April 2026:** map viewer and website redesign; 14 reporting points added (Pakistan, Madagascar). No impact on model results.
- **v4.4 — 10 September 2025:** AIFS Single introduced as meteorological forcing to 15-day lead time.
- **v4.3 — 4 June 2025:** operational forecast configuration moved to 46-day sub-seasonal; medium-range web products reduced to 15 days; 30-day forecasts retained on EWDS.
- **v4.2 — 12 November 2024**
- **v4.1 — 28 February 2024**
- **v4.0 — 26 July 2023 (major):** resolution raised from 0.1° to 0.05°; new input map set; LISFLOOD improvements; recalibration at 1995 gauges; new return-period thresholds on a 1979–2022 climatology; flood hazard maps to ~90 m.
- **v3.5 — 28 June 2023**; earlier versions from 2018. Full chronology: https://confluence.ecmwf.int/display/CEMS/GloFAS+versioning+system

---

## Official documentation
- Dataset page: https://ewds.climate.copernicus.eu/datasets/cems-glofas-forecast
- GloFAS wiki: https://confluence.ecmwf.int/display/CEMS/Global+Flood+Awareness+System
- Model output specifications: https://confluence.ecmwf.int/display/CEMS/Model+Output
- Meteorological forcing: https://confluence.ecmwf.int/display/CEMS/GloFAS+meteorological+forecasts
- Versioning system: https://confluence.ecmwf.int/display/CEMS/GloFAS+versioning+system
- Latest operational release: https://confluence.ecmwf.int/display/CEMS/Latest+operational+GloFAS+release
- Auxiliary data guide: https://confluence.ecmwf.int/display/CEMS/Auxiliary+Data
- Known issues: https://confluence.ecmwf.int/display/CEMS/GloFAS+-+Known+Issues
- Web portal: https://www.globalfloods.eu/
- Licence text: https://object-store.os-api.cci2.ecmwf.int/cci2-prod-catalogue/licences/cems-floods/cems-floodsv1_34fd7d51830f6470b596eb16b6cf9a4e36605fe3acf3c2406e021591499e8c32.md

### Key reference
- Alfieri, L. et al. (2013). GloFAS – global ensemble streamflow forecasting and flood early warning. *Hydrology and Earth System Sciences*, 17, 1161–1175.
- Harrigan, S. et al. (2020). GloFAS-ERA5 operational global river discharge reanalysis 1979–present. *Earth System Science Data*, 12, 2043–2060.
