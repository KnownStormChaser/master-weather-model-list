# ICON-CH-EPS (Switzerland)

## What this model is
ICON-CH-EPS is the operational **high-resolution regional ensemble** numerical weather prediction (NWP) system run by MeteoSwiss for Switzerland and the wider Alpine region. It comprises two nested convection-permitting ensemble configurations — **ICON-CH1-EPS** (~1 km) and **ICON-CH2-EPS** (~2.1 km) — both built on the ICON model and run as ensembles to represent forecast uncertainty explicitly.

The systems are designed for the topographically challenging Alpine region, where MeteoSwiss positions Switzerland at the centre of the model domain. They are run several times a day on the Alps supercomputer at the Swiss National Supercomputing Centre (CSCS).

---

## Who runs it
- **Organization:** MeteoSwiss (Federal Office of Meteorology and Climatology)
- **Country / region:** Switzerland
- **Compute:** Swiss National Supercomputing Centre (CSCS), "Alps" platform
- **Model development:** ICON is developed jointly by DWD and the Max Planck Institute for Meteorology; MeteoSwiss participates via the C2SM (ETH Zurich et al.) and the COSMO consortium

---

## What area it covers
- **Coverage:** The entire Alpine region, with Switzerland at the centre of the domain
- **Bounding box:** ICON-CH1-EPS −0.82°E / 42.03°N → 17.71°E / 50.50°N; ICON-CH2-EPS −0.77°E / 42.08°N → 17.68°E / 50.48°N (STAC collection extents; confirmed against the `CLON`/`CLAT` ranges in the horizontal constants files)
- **Domain note:** The two configurations cover the same nominal region but are *not* the same grid — they differ in horizontal resolution, bounding box, ensemble size, forecast length, and update cadence, and carry different `uuidOfHGrid` values (`numberOfGridUsed` = 1 for ICON-CH1-EPS, 2 for ICON-CH2-EPS)

---

## Basic details
- **Model type:** Regional ensemble NWP (convection-permitting)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic), limited-area configuration
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** Yes — deep convection is explicitly resolved at both 1 km and 2.1 km; no deep-convection parameterization
- **Horizontal resolution:** ~1 km (ICON-CH1-EPS) / ~2.1 km (ICON-CH2-EPS), on the native icosahedral grid
- **Vertical levels:** 80 full levels (81 half levels), terrain-following height-based coordinate (Lorenz staggering), model top at exactly 22,000 m (flat). Levels are numbered from top to bottom
- **Model topography:** Reaches 4,440 m at its highest point (ICON-CH1-EPS; `HSURF` max = 4,439.98 m, min = −14.65 m)
- **Horizontal grid cells:** 1,147,980 (ICON-CH1-EPS) / 283,876 (ICON-CH2-EPS) triangle centres — verified from the GRIB2 `numberOfDataPoints` key
- **Total grid points (3D):** ~91,838,400 (ICON-CH1-EPS) / ~22,710,080 (ICON-CH2-EPS) — i.e. horizontal cells × 80 full levels
- **Internal model time step:** 10 s (ICON-CH1-EPS) / 20 s (ICON-CH2-EPS)
- **Temporal output resolution:** Hourly (both)

### ICON-CH1-EPS
- **Ensemble members:** 11 (1 control + 10 perturbed)
- **Forecast length:** Up to 33 hours (the 03 UTC run is extended to 45 hours to cover the whole of the following day)
- **Update frequency:** Every 3 hours — 8× daily (00, 03, 06, 09, 12, 15, 18, 21 UTC)

### ICON-CH2-EPS
- **Ensemble members:** 21 (1 control + 20 perturbed)
- **Forecast length:** Up to 120 hours (5 days)
- **Update frequency:** Every 6 hours — 4× daily (00, 06, 12, 18 UTC)

---

## Data assimilation
- **System:** KENDA (Kilometre-scale Ensemble Data Assimilation)
- **Method:** Local Ensemble Transform Kalman Filter (LETKF), producing an analysis ensemble that supplies the slightly differing initial conditions for the forecast ensemble
- **Cadence / grid:** The analysis ensemble is cycled **hourly** on the ICON-CH1-EPS 1 km / 80-level grid. ICON-CH2-EPS initial conditions are obtained by **upscaling** (interpolating) the ICON-CH1-EPS analysis to the 2.1 km grid every 6 hours
- **Radar:** Weather-radar data are assimilated via **latent heat nudging (LHN)**, particularly valuable in the first forecast hours
- **Observations:** Ground-based stations, radiosoundings, aircraft (AMDAR and Mode-S), wind profilers/radar, wind lidar, and ship/buoy reports
- **Analysis product:** The KENDA-CH1 analysis is also published separately via Open Data (collection `ch.meteoschweiz.ogd-analysis-kenda-ch1`)

---

## Initial and boundary conditions
- **Initial conditions:** KENDA / LETKF analysis ensemble (see above)
- **Boundary conditions:** ECMWF **IFS ENS** global ensemble (~9 km), which drives both ICON-CH1-EPS and ICON-CH2-EPS

---

## What it provides
Probabilistic (ensemble) forecasts including:
- Surface and near-surface fields: temperature, humidity, wind, gusts, mean sea level pressure, precipitation, cloud cover
- Upper-air fields on 80 model levels (multi-level parameters)
- Both the **full ensemble** (perturbed members) and the **unperturbed control run**
- **Pollen concentrations** (ICON-CH2-EPS control run only — a `forecast:perturbed=true` search returns nothing): specific number concentration on the lowest full model level (`model_level=80`, `typeOfLevel=generalVerticalLayer`, ~first 20 m above ground). Each type is generated only during its flowering season, so out-of-season variables are simply absent from both the data and the parameter CSV:

  | Pollen type | Variable | Season window |
  |---|---|---|
  | Alder | `ALNUsnc` | Jan 8 – Mar 31 |
  | Hazel | `CORYsnc` | Jan 8 – Mar 17 |
  | Birch | `BETUsnc` | Mar 18 – May 25 |
  | Grasses | `POACsnc` | Apr 1 – Aug 31 |
  | Ragweed | `AMBRsnc` | Jul 9 – Sep 30 |

  The same pollen variables are also published in the hourly KENDA-CH1 analysis dataset.

Constant fields are distributed as separate static files, refreshed alongside the model runs:
- **Horizontal constants** (17 GRIB2 messages, verified by decoding `horizontal_constants_icon-ch1-eps.grib2`): `CLON`, `CLAT` (triangle-centre longitude/latitude), `HSURF` (surface height), `FR_LAND` (land/sea), `FR_LAKE`, `FR_ICE`, `DEPTH_LK` (lake depth), `SOILTYP` (1–9), `PLCOV` (plant cover), `LAI`, `ROOTDP`, `FOR_D`, `SKC` (skin conductivity), and the four sub-grid orography fields `SSO_STDH`, `SSO_GAMMA`, `SSO_THETA`, `SSO_SIGMA`
- **Vertical constants** (81 GRIB2 messages): `HHL`, the geometric height of each half level above mean sea level. Level 1 is the flat model top at exactly 22,000 m; level 81 equals `HSURF`
- **Grid consistency check:** MeteoSwiss recommends verifying that the `uuidOfHGrid` and `uuidOfVGrid` GRIB keys in the static files match those in the forecast file before pairing them

---

## Data availability
- **Is the data free?** Yes
- **License:** CC BY 4.0 (MeteoSwiss Open Data; attribution required) — https://opendatadocs.meteoswiss.ch/general/terms-of-use
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (GRIB edition 2). Stock ecCodes decodes most fields to generic WMO shortNames (`2t`, `tp`, `lsm`, `sdor`, `isor`, `anor`, `slor`, `lai`, `ci`, `cl`), but returns `shortName = 'unknown'` for the COSMO-local parameters — `SOILTYP`, `DEPTH_LK`, `ROOTDP`, `FOR_D` and the pollen fields. Use the **COSMO consortium local definitions** (`eccodes-cosmo-resources`) to get COSMO shortNames throughout and to resolve those parameters
- **Grid:** Native icosahedral grid only — forecast GRIB files do **not** contain lat/lon or height; geolocation requires the static horizontal/vertical constants files
- **File granularity:** One file per collection / reference time / lead time / variable / control-or-perturbed. The **control** file holds a single GRIB2 message (`perturbationNumber = 0`); the **perturbed** file holds 10 (ICON-CH1-EPS) or 20 (ICON-CH2-EPS) messages, one per member. `numberOfForecastsInEnsemble` is 11 / 21 in both, counting the control
- **Member ordering caveat:** `perturbationNumber` values inside a perturbed file are **not** in ascending order (an observed ICON-CH1-EPS ordering was 8, 3, 4, 10, 7, 6, 2, 5, 9, 1). Members must be identified by the GRIB key, not by message position
- **Parameter inventory:** 118 parameters for ICON-CH1-EPS (16 multi-level), 121 for ICON-CH2-EPS (17 multi-level), published as a CSV in each collection's STAC assets. The sets are **not** identical: `DBZ` and `SI` are ICON-CH1-EPS only; `DEN`, `DHAIL_MX`, `LPI_MAX` and the pollen fields are ICON-CH2-EPS only
- **GRIB2 encoding:** centre `lssw`, `tablesVersion` 15, `localTablesVersion` 1, grid definition template 101 (unstructured), `grid_simple` packing at 16 bits per value. Product definition templates: 1 (instantaneous ensemble), 11 (accumulated ensemble), 41 (ensemble atmospheric chemical constituent — pollen)
- **Typical file sizes:** ICON-CH1-EPS — single-level 199 B – 2.2 MiB (control) / 1.9 KiB – 22.4 MiB (perturbed); multi-level 19.7 – 177.4 MiB (control) / 197.1 MiB – 1.7 GiB (perturbed). ICON-CH2-EPS — single-level 175 B – 564.7 KiB / 3.4 KiB – 11 MiB; multi-level 4.9 – 43.9 MiB / 97.5 – 877.5 MiB
- **Retention:** **24 hours only.** Files are removed after the availability window; requests for older data return an empty response or HTTP 403. Each STAC item carries an `expires` property set to exactly 24 h after `created`. There is no official long-term archive — users building archives must retain files themselves
- **Boundary note:** Values at the spatial domain boundary may be unreliable
- **Official download location / docs:**
  - https://opendatadocs.meteoswiss.ch/e-forecast-data/e2-e3-numerical-weather-forecasting-model
  - STAC collections:
    - ICON-CH1-EPS: `ch.meteoschweiz.ogd-forecasting-icon-ch1`
    - ICON-CH2-EPS: `ch.meteoschweiz.ogd-forecasting-icon-ch2`

### Access methods
MeteoSwiss exposes the data through the Swiss federal geodata STAC API at `data.geo.admin.ch`. Three documented routes:
1. **Python API** — the `meteodata-lab` library (`meteodatalab.ogd_api`), which loads GRIB2 directly into xarray and supports `ref_time="latest"`. Recommended by MeteoSwiss; it also fetches the `CLON`/`CLAT` coordinates automatically.
2. **REST API (HTTP POST)** — the STAC search endpoint (see below).
3. **STAC Browser** — manual browsing/download per collection.

### Retrieving a forecast file (REST API)
Resolve the download URL with a POST to the STAC search endpoint:

```
POST https://data.geo.admin.ch/api/stac/v1/search
Content-Type: application/json

{
  "collections": ["ch.meteoschweiz.ogd-forecasting-icon-ch1"],
  "forecast:reference_datetime": "2026-07-29T03:00:00Z",
  "forecast:variable": "TOT_PREC",
  "forecast:perturbed": false,
  "forecast:horizon": "P0DT12H00M00S"
}
```

- `forecast:reference_datetime` — model run initialization time (ISO 8601, `Z`)
- `forecast:variable` — the parameter shortName (e.g. `T_2M`, `TOT_PREC`, `U_10M`, `PMSL`)
- `forecast:perturbed` — `false` for the control run, `true` for the perturbed ensemble members
- `forecast:horizon` — lead time as an ISO 8601 duration, e.g. `P0DT00H00M00S` for +0 h, `P0DT12H00M00S` for +12 h

All four filters are optional; omitting them broadens the search. The response is a STAC FeatureCollection, paginated at 100 features with a `next` link. The GRIB file is the pre-signed URL in each feature's `assets[].href`. Files are served from the CSCS object store (`rgw.cscs.ch`), not from `data.geo.admin.ch`.

Downloads are plain GRIB2 with no extra compression, and carry useful `x-amz-meta-*` headers — `model`, `param`, `step`, `date`, `time`, `perturbed`, and `sha256` for integrity checking.

Static grid files are listed on the collection object itself, or via a dedicated endpoint:

```
GET https://data.geo.admin.ch/api/stac/v1/collections/ch.meteoschweiz.ogd-forecasting-icon-ch1/assets
```

Three assets per collection: `horizontal_constants_*.grib2`, `vertical_constants_*.grib2`, and `params_*.csv`.

Because the published data sits on the native icosahedral grid (one value per triangle centre), any regular-grid product must be interpolated by the user from the `CLON`/`CLAT` coordinates; elevation is available from `HSURF`, with land masking via `FR_LAND`.

### Parameter naming
GRIB shortNames follow DWD/COSMO conventions — e.g. `T_2M` (2 m temperature), `TD_2M` (2 m dew point), `TOT_PREC` (total precipitation), `PMSL` (mean sea level pressure), `U_10M`/`V_10M` (10 m wind), `VMAX_10M` (gusts), `CLCT`/`CLCL`/`CLCM`/`CLCH` (cloud cover), `ASWDIR_S`/`ASWDIFD_S` (direct/diffuse shortwave radiation), `CAPE_ML`, `CIN_ML`, `HZEROCL`, `SNOWLMT`, `H_SNOW`, `T_G`. Each collection publishes a continuously updated "Overview of Parameters" CSV (long name, unit, level type, aggregation, etc.) in its STAC assets.

Accumulated fields (`TOT_PREC`, `RAIN_GSP`, `SNOW_GSP`, `GRAU_GSP`, `RUNOFF_S`, `RUNOFF_G`, `DURSUN`) accumulate from the reference time; `TMAX_2M`, `TMIN_2M` and `VMAX_10M` are extrema over the previous hour.

---

## Notes
- **Analysis equivalence:** MeteoSwiss does not distribute analysis fields within these forecast collections. The forecast at **lead time 0** (`P0DT00H00M00S`) is the analysis equivalent — available every 3 h (ICON-CH1-EPS) or 6 h (ICON-CH2-EPS). The full hourly analysis ensemble is published separately as KENDA-CH1.
- **Native grid only:** Unlike DWD's ICON Open Data (which also offers pre-interpolated lat/lon grids), MeteoSwiss publishes only the native icosahedral grid; any regular-grid product must be interpolated by the user.
- **Relationship to other ICON systems:** ICON-CH-EPS shares the ICON dynamical core and non-hydrostatic formulation with DWD's [ICON Global](../../global/germany/icon-global.md) family. It is comparable in role and resolution to DWD's [ICON-D2-EPS](../../../ensemble_models/regional/de/icon-d2-eps.md) convection-allowing ensemble (2.2 km, 20 members), though ICON-CH1-EPS pushes to ~1 km.
- **Output is probabilistic:** Results should be interpreted as an ensemble (member spread / probabilities), not a single deterministic forecast.
- **Known errors in the official parameter CSV** (verified against decoded GRIB2, July 2026): the pollen rows give `Standard Unit` as `60`, which is the GRIB2 `parameterNumber` for specific number concentration rather than a unit; and `TOT_PREC` is listed as `kg m-2 s-1` although the GRIB message decodes as `kg m**-2` accumulated from reference time (`stepRange 0-6`, PDT 11). Trust the GRIB keys over the CSV for units.
- **Documented vs. actual forecast length:** the MeteoSwiss summary table states 33 h for ICON-CH1-EPS without qualification. Live STAC queries confirm the 03 UTC cycle genuinely extends to 45 h while all other cycles stop at 33 h.
- **24 h retention** is the most operationally significant constraint for downstream archiving.

---

## Official documentation
- MeteoSwiss Open Data — ICON-CH1/2-EPS: https://opendatadocs.meteoswiss.ch/e-forecast-data/e2-e3-numerical-weather-forecasting-model
- ICON-CH1/2-EPS changelog: https://opendatadocs.meteoswiss.ch/e-forecast-data/e2-e3-numerical-weather-forecasting-model-changelog
- ICON forecasting systems overview: https://www.meteoswiss.admin.ch/weather/warning-and-forecasting-systems/icon-forecasting-systems.html
- Ensemble data assimilation (KENDA): https://www.meteoswiss.admin.ch/weather/warning-and-forecasting-systems/icon-forecasting-systems/ensemble-data-assimilation.html
- KENDA-CH1 analysis data: https://opendatadocs.meteoswiss.ch/e-forecast-data/e5-numerical-weather-analysis-data
- Terms of use (CC BY 4.0): https://opendatadocs.meteoswiss.ch/general/terms-of-use
- Example notebooks (data retrieval to visualization): https://github.com/MeteoSwiss/opendata-nwp-demos
