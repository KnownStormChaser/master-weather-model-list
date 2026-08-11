# EFAS Seasonal (European Flood Awareness System — seasonal forecast)

## What this model is
EFAS Seasonal is the long-range configuration of the Copernicus Emergency Management Service European river discharge forecast. It runs the LISFLOOD hydrological model at **1 arcminute** (~1.4 km) across the European domain, forced with ECMWF's SEAS5 seasonal ensemble, initialised once a month and extending to 215 days.

It forecasts **discharge**, not water level.

This is the finest-resolution operational hydrological forecast in the catalogue, roughly 3.5 times finer than the global GloFAS chain. It is also, notably, **the only openly available EFAS forecast product**: the EFAS medium-range forecast is embargoed by 30 days with real-time access restricted to EFAS partner institutions, while this seasonal product carries no such restriction — see *Notes*.

> **This entry is filed under `hydrological_models/` rather than `climate_models/`**, following the repository's convention that seasonal forecasts of a phenomenon use the phenomenon's template. It uses `hydrological-model.template.md` with the optional **Long-range configuration** section retained, and points upstream to [SEAS5](../../../climate_models/global/eu/seas5.md) rather than duplicating that system's coupled configuration.

---

## Who runs it
- **Organization:** Copernicus Emergency Management Service (CEMS) — European Commission Joint Research Centre (JRC), implemented by ECMWF
- **Country / region:** International (European Union programme); domain covers Europe and adjacent regions

---

## What area it covers
- **Coverage:** Europe and adjacent regions — from northern Africa to beyond the northern tip of Scandinavia, west into the Atlantic and east to the Caspian Sea. The domain was enlarged at v5.0 to match complete river catchments rather than political boundaries.
- **Exact domain bounds:** **TBD** — ECMWF documents the extent only qualitatively. No numeric bounding box is published on the CEMS pages, and the EWDS catalogue record is unusable (see *Notes*). Needs reading from a file header or from the auxiliary NetCDF.
- **Grid dimensions:** TBD — depends on the bounds above
- **Projection:** EPSG:4326 (regular latitude–longitude) for v5.0 onward. Versions 4.0 and older used ETRS89 Lambert Azimuthal Equal Area (EPSG:3035) — a **projection change, not just a resolution change**, so pre-v5.0 and post-v5.0 files are not directly stackable.

---

## Basic details
- **Model type:** Ensemble hydrological model (seasonal range)
- **Core hydrological model:** LISFLOOD (open source; JRC)
- **Forecast length:** 5160 h (215 days), 215 lead-time steps at 24-hour intervals — verified against the EWDS request constraints
- **Update frequency / cycles:** monthly, initialised on the 1st at 00 UTC
- **Temporal output resolution:** 24 h
- **Operational system version:** EFAS v5.6, launched 25 February 2026 (first v5.6 forecast: 2026-02-25 12:00 UTC). Individual forecasts carry their production version in the file metadata, so an archive spanning several months spans several versions.

---

## Grid and river network
- **Land surface grid:** 1 × 1 arcminute (0.0166667°, ~1.4 km at EFAS latitudes). Raised from 5 × 5 km at v5.0 (20 September 2023).
- **Routing geometry:** Gridded routing on the same 1-arcminute grid; no reach-indexed channel product
- **Hydrography / DEM source:** new 0.0166667° input map set at v5.0; flood hazard maps use MERIT Hydro at 90 m (those are viewer products, not distributed grids)
- **Calibration:** 1903 in-situ gauging stations, with parameter regionalisation for ungauged catchments (v5.0)
- **Soil layers:** three, represented explicitly in LISFLOOD, with per-layer soil depth, wilting point and field capacity distributed as auxiliary data
- **Reservoir and lake representation:** TBD

---

## Meteorological forcing
- **Driving atmospheric model:** [SEAS5](../../../climate_models/global/eu/seas5.md) — ECMWF's seasonal forecasting system, 51 members at ~36 km
- **Forcing quantity:** seasonal meteorological ensemble forecasts. Note this differs from [GloFAS Seasonal](../../global/eu/cems-glofas-seasonal.md), which is forced with *downscaled runoff* from SEAS5 rather than with meteorology — the two seasonal chains are not the same design at different resolutions.
- **Downscaling:** TBD — SEAS5 at ~36 km driving LISFLOOD at ~1.4 km is a ratio of roughly 26:1, so the downscaling method matters considerably here. Not documented on the public pages.
- **Bias correction:** TBD

---

## Initialization and antecedent state
- **Initial state source:** the operational EFAS chain at the start of the month (TBD — not documented for the seasonal configuration specifically)
- **Satellite soil moisture:** EFAS ingests an H SAF satellite soil moisture product for initial conditions, updated to H SAF 26 at ~10 km in v5.6. **TBD — whether this enters the seasonal chain's initial state or only the medium-range chain is not stated.**
- **Assimilates discharge observations:** TBD
- **No-DA variant published?** No

---

## Ensemble configuration
- **Ensemble size:** 51
- **Source of perturbations:** inherited entirely from SEAS5. The hydrological model is not perturbed.
- **Member packaging:** no `product_type` selector in the request form — control and perturbed members are not separable at request time
- **Derived products distributed:** none as raw data. Monthly discharge anomaly categories on EFAS-IS are viewer products — out of scope.

---

## Long-range configuration
- **Driving long-range system:** [SEAS5](../../../climate_models/global/eu/seas5.md)
- **Initialization cadence:** 1st of each month
- **Hindcast (reforecast) period:** TBD — the EFAS v5.0 technical table records Reforecasts as "tbc", and no period is published. The companion `efas-seasonal-reforecast` dataset on EWDS is the place to establish this.
- **Reference climatology period:** 1992-01-01 to 2022-12-31 for thresholds and anomalies (EFAS v5.0 reference climatology). Note this is a **shorter and later** baseline than GloFAS v4's 1979–2022 — the two systems' anomalies are not computed against comparable climatologies.
- **Hindcasts distributed alongside forecasts?** No — separate dataset, `efas-seasonal-reforecast`. Anomaly products use a forecast-range-dependent climatology derived from the reforecasts.
- **Sources of predictability:** TBD

---

## What it provides
- **River discharge in the last 24 hours** (m³ s⁻¹, surface level)
- **Volumetric soil moisture** (three soil levels)
- **Snow depth water equivalent** (surface)
- **Soil wetness index (root zone)** (sub-surface)
- **Runoff water equivalent (surface plus subsurface)** (surface and sub-surface)

The request form exposes a `model_levels` selector (`surface_level` / `soil_levels`) and a `soil_level` selector (1, 2, 3), so soil variables are retrieved per layer.

**Time-invariant auxiliary data**, version-matched and required for interpretation — a fuller set than GloFAS provides:
- Upstream area
- Elevation
- Soil depth (3 levels)
- Wilting point (3 levels)
- Field capacity (3 levels)

The request form carries separate auxiliary variable groups for system versions **5.0, 4.0, 3.5, 3.0 and 2.0**. Match the auxiliary version to the forecast version — the upstream area map is specific to the hydrological model configuration and changes between cycles.

---

## Data availability
- **Is the data free?** Yes, with free self-registration on the CEMS Early Warning Data Store
- **License:** **CEMS-FLOODS datasets licence** — free reproduction, distribution, communication to the public, and adaptation. Attribution required: *"Generated using Copernicus Emergency Management Service information [Year]"*, or *"Contains modified Copernicus Emergency Management Service information [Year]"* for adapted data. No share-alike or non-commercial restriction. The EWDS catalogue API reports `license: other`; full text linked below.
  - **Note.** The licence's Article 12 restricted-data clause — access granted only to specific registered users — is what governs the embargoed EFAS medium-range and historical products. It does **not** apply to this dataset.
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded fields only
- **Real-time availability:** **Current, no embargo.** Catalogue temporal extent ran to 2026-08-01 when checked on 2026-08-11, consistent with monthly initialisation. `cads:update_frequency` is `Monthly`. Neither the EWDS record nor the CEMS Model Output page attaches a partner restriction to the seasonal product, in contrast to the EFAS historical and medium-range entries on the same page, which both carry one explicitly.
- **Data formats:** GRIB and NetCDF-4. The NetCDF-4 files inherit the WMO GRIB2 conventions rather than following CF independently.
  - **Note.** The request form's format values are `grib` / `netcdf` here, against `grib2` / `netcdf` on the GloFAS datasets. Cosmetic, but it will break a request builder shared across the two.
- **Official download location:**
  https://ewds.climate.copernicus.eu/datasets/efas-seasonal
- **DOI:** https://doi.org/10.24381/cds.eb224b0e
- **Archive extent:** 2020-11-01 to present
- **Access route notes:** API request against the EWDS processes endpoint with an account key, or the web form. No anonymous directory tree.

> **Verification status.** Verified live against the EWDS catalogue, request form, constraints and licence endpoints, plus ECMWF CEMS documentation. **No GRIB or NetCDF file was decoded** — EWDS retrieval requires an authenticated account key — so the domain bounds, grid dimensions, GRIB parameter encoding and packing remain unconfirmed. Establishing the numeric domain extent is the highest-value follow-up here, since it is not published anywhere in the documentation.

---

## Notes

- **This is the only open EFAS forecast product.** The CEMS Model Output page states for EFAS Historical that data is available "with a delay of 6 days. The real-time data is only available to EFAS partners", and for EFAS Real-time Forecasts that data is available "up until present with a 30-day delay. The real-time data is only available to EFAS partners." The corresponding paragraph for EFAS Seasonal carries **no such sentence**, and the live catalogue confirms it: the seasonal record was current to 2026-08-01 while the `efas-forecast` record trailed by roughly five weeks. The embargo is product-specific, not an EFAS-wide policy — which is why the medium-range EFAS forecast is excluded from this catalogue and this one is not.

- **The v5.0 change was a projection change as well as a resolution change.** Pre-v5.0 files are on ETRS89-LAEA (EPSG:3035) in metres; v5.0 onward are on EPSG:4326 in degrees. Auxiliary data follows the same split. Any workflow reading across the September 2023 boundary needs to reproject, not merely regrid.

- **No numeric domain extent is published.** ECMWF describes the domain in prose only. The EWDS catalogue record is worse than unhelpful: it reports `bbox: [0.0, -70.0, 360.0, 70.0]` for a European domain — a global 0–360 box that excludes most of Scandinavia at the top and includes the southern ocean at the bottom. Read the bounds from an auxiliary NetCDF instead.

- **Discharge accumulation semantics.** *River discharge in the last 24 hours* is a daily mean over the preceding window, not an instantaneous value at the timestamp.

- **Documentation gap — soil wetness index availability.** The CEMS Model Output page annotates soil wetness index as available "from EFAS v5.1" and volumetric soil moisture as "EFAS v4 and previous", implying the two variables swapped over. Both appear in the current request form's time-variant group. **TBD — whether volumetric soil moisture is still produced for recent initialisations, or is retained only for the legacy archive.** The historical dataset's note that volumetric soil moisture is "provided until 2024-03-20" suggests the latter.

- **Version spread across the archive.** Operational forecasts use whatever EFAS version was current at initialisation, and the version is recorded in file metadata rather than in the request. An archive spanning 2020-11 to present crosses v4.0, v5.0 and the v5.1–v5.6 minors, including the projection change. Do not treat the archive as homogeneous.

- **Relationship to other CEMS-Flood datasets.** Global counterpart: [GloFAS Seasonal](../../global/eu/cems-glofas-seasonal.md) — but note the forcing design differs (meteorology here, downscaled runoff there). The EFAS medium-range forecast (`efas-forecast`), historical simulation (`efas-historical`), and the two reforecast datasets are all either embargoed or historical-only and are out of scope as standalone entries. EFAS also runs a daily sub-seasonal (S2S) outlook to 6 weeks that GloFAS does not have; **TBD — whether any raw grid from it reaches EWDS**, since it does not appear in the current dataset list.

- **AI relationship.** None identified. The AIFS Single forcing introduced in the GloFAS chain at v4.4 has no documented EFAS counterpart. If EFAS adopts AI forcing, update [`AI_MODELS.md`](../../../../AI_MODELS.md).

- **Not a flood warning.** The licence states that CEMS-Flood data does not constitute a flood warning, which only national and regional institutions are authorised to issue within their areas of responsibility.

---

## Recent version history
- **v5.6 — 25 February 2026:** EFAS-IS map viewer redesign; satellite soil moisture updated to H SAF 26 at ~10 km; rainfall animation and visualisation changes.
- **v5.4 — 12 March (year TBD):** CEMS-Flood long-range product design harmonised with GloFAS v4.3.
- **v5.0.0 — 20 September 2023 (major):** resolution 5 km → 1 arcminute; projection ETRS89-LAEA → EPSG:4326; enlarged domain matched to river catchments; new input maps; LISFLOOD improvements; recalibration at 1903 gauges; new thresholds on a 1992–2022 climatology; LISFLOOD-FP flood hazard maps at 90 m; ERIC moved to LISFLOOD surface runoff output.
- **v4.0.0 — 14 October 2020**
- **v3.5.0 — 5 March 2020** (recalibration over Albania; new climatology)
- **v3.0.0 — 13 May 2019**; **v2.0.0 — 10 October 2018**; **v1.0.0 — 16 May 2018** (new domain).

Full chronology: https://confluence.ecmwf.int/display/CEMS/EFAS+versioning+system

---

## Official documentation
- Dataset page: https://ewds.climate.copernicus.eu/datasets/efas-seasonal
- EFAS wiki: https://confluence.ecmwf.int/display/CEMS/European+Flood+Awareness+System
- Model output specifications: https://confluence.ecmwf.int/display/CEMS/Model+Output
- EFAS v5.0 release notes: https://confluence.ecmwf.int/display/CEMS/EFAS+v5.0
- Latest operational release: https://confluence.ecmwf.int/display/CEMS/Latest+operational+EFAS+release
- Versioning system: https://confluence.ecmwf.int/display/CEMS/EFAS+versioning+system
- Sub-seasonal and seasonal forecasting: https://confluence.ecmwf.int/display/CEMS/CEMS-Flood+sub-seasonal+and+seasonal+forecasting
- Auxiliary data guide: https://confluence.ecmwf.int/display/CEMS/Auxiliary+Data
- Web portal: https://european-flood.emergency.copernicus.eu/ and https://www.efas.eu
- Licence text: https://object-store.os-api.cci2.ecmwf.int/cci2-prod-catalogue/licences/cems-floods/cems-floodsv1_34fd7d51830f6470b596eb16b6cf9a4e36605fe3acf3c2406e021591499e8c32.md
