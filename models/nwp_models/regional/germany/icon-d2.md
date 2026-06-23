# ICON-D2 (DWD convection-permitting regional model)

## What this model is
ICON-D2 is DWD's convection-permitting (high-resolution) regional NWP configuration of ICON, run as a limited-area model over central Europe.

At 2.2 km horizontal resolution it resolves deep convection explicitly, making it well suited to short-range forecasting of small-scale, high-impact weather such as convective precipitation, thunderstorms, strong winds, and fog. ICON-D2 replaced COSMO-D2 on 10 February 2021 as part of DWD's migration from the COSMO model family to ICON.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany

---

## What area it covers
- **Coverage:** Central Europe — Germany, Switzerland, Austria, the Benelux countries, and parts of neighbouring countries

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** Yes (2.2 km; deep convection resolved explicitly)
- **Horizontal resolution:** 2.2 km (native triangular grid, ~2.1 km average grid spacing — see flag in Notes)
- **Public output grids:** Native triangular grid; also distributed on a rotated lat-lon grid at 0.02° (same spacing as the former COSMO-D2 lat-lon output)
- **Vertical levels:** 65 (directly confirmed by DWD's EWGLAM 2024/2025 and ICCARUS 2025 operational-chain posters; same as ICON-D2-RUC and ICON-D2-EPS)
- **Model top:** TBD (see flag in Notes)
- **Forecast length:** Up to +48 hours
- **Update frequency / cycles:** 8× daily, every 3 hours (00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:** Hourly
- **Cloud microphysics:** Single-moment bulk scheme (the rapid-update sibling ICON-D2-RUC uses the more advanced two-moment scheme)

---

## Data assimilation
ICON-D2 runs a **3-hourly KENDA-LETKF** data assimilation cycle. The DA stream ingests conventional surface and upper-air observations, aircraft and radiosonde data, satellite observations, and radar information, including Latent Heat Nudging (LHN) from radar-derived precipitation. (The rapid-update [ICON-D2-RUC](./icon-d2-ruc.md) tightens this to an hourly cycle.)

---

## Initial and boundary conditions
- **Initial conditions:** From ICON-D2's own 3-hourly KENDA-LETKF analysis.
- **Boundary conditions:** Lateral boundaries supplied by [ICON-EU](./icon-eu.md). Unlike the global↔ICON-EU two-way nest, ICON-D2 is a one-way-driven limited-area model.

---

## What it provides
High-resolution deterministic forecasts of:
- temperature, wind, precipitation, humidity, pressure, cloud and hydrometeor fields
- convection-allowing severe-weather products: thunderstorm and hail indicators, Lightning Potential Index (LPI), heavy-precipitation fields
- simulated radar reflectivity
- aviation-relevant fields (migrated from COSMO-D2 in February 2021)

---

## Data availability
- **Is the data free?** Yes
- **License:** CC BY 4.0 (DWD Open Data licence; attribution required)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (native triangular grid and rotated lat-lon at 0.02°)
- **Official download location:**  
  https://opendata.dwd.de/weather/nwp/icon-d2/
- **Retention note:** DWD retains GRIB2 files on the open data server for a short period only (typically 24 hours). Users needing longer-term archives must set up their own retention.

---

## Notes
- ICON-D2 is the convection-permitting limited-area member of DWD's ICON suite, driven by [ICON-EU](./icon-eu.md) boundaries. Its rapid-update sibling is [ICON-D2-RUC](./icon-d2-ruc.md) (same core, same domain, same grid, but hourly cycling, +14 h range, and two-moment microphysics).
- Ensemble counterparts: [ICON-D2-EPS](../../../ensemble_models/regional/de/icon-d2-eps.md) (3-hourly, +48 h) and [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md) (hourly, +14 h).
- The ICON model code has been open source under a permissive licence since January 2024 (repository: https://gitlab.dkrz.de/icon/icon-model).
- Standard ICON-D2 is distributed under the main `/weather/nwp/icon-d2/` path; the newer `/weather/nwp/v1/m/` distribution tier (introduced 2025) hosts ICON-D2-RUC and the ICON-ART family rather than standard ICON-D2.
- **Flag — horizontal resolution wording:** the previous entry stated "~2.1 km average grid spacing," while sibling entries (ICON-D2-RUC, ICON-D2-EPS) consistently cite 2.2 km. I've reconciled these by giving 2.2 km with the ~2.1 km native average noted; confirm preferred phrasing.
- **Flag — model top:** ICON-D2's model top is not stated in current sources (left TBD); worth confirming against DWD's Database Reference. (Vertical-level count of 65 is now directly confirmed by DWD operational-chain posters.)
- ICON-D2 spawns a 500 m nested domain, **ICON-D05**, operational since 27 February 2025. ICON-D05 is initialised from the operational ICON-D2 analysis (via intermediate 1 km nesting). Confirm whether ICON-D05 output is distributed on the DWD open data server before adding a standalone entry.

---

## Recent version history

### 27 February 2025 — ICON-D05 500 m nest operational
A 500 m nested domain (ICON-D05) was added, initialised from the operational ICON-D2 analysis.

### 5 February 2025 — model version icon-2024.10-dwd-2.1 (effective with the 09 UTC run)
- **Reduced overestimation of high precipitation intensities** (standard single-moment ICON-D2 only; not RUC): supersaturation-aware saturation adjustment, restricted raindrop-size-distribution shift, reduced snow-to-graupel riming conversion, and retuned convection to suppress spurious "convective drizzle." Primarily affects the May–September convective season.
- **TERRA_URB urban canopy scheme activated** (all configurations): urban heat-island / dry-island effects now represented; nighttime urban 2 m temperatures warm by up to ~1 K, with RMSE reductions of up to ~4 % around cities such as Paris.
- **Assimilation of meteorological ICOS tower data** (all configurations): temperature, humidity and selected wind levels from 9 ICOS towers over Germany; local, short-lived forecast impact.
- **Retuned visibility diagnostic** (all configurations): corrects nighttime fog underestimation; visibility diagnostic itself was introduced in ICON-D2 in spring 2024.

### 10 February 2021 — ICON-D2 replaces COSMO-D2
ICON-D2 became DWD's operational convection-permitting regional model, replacing COSMO-D2 as part of the migration from the COSMO model family to ICON. Aviation weather products were migrated from COSMO-D2 at the same time. Older references may still use the COSMO-D2 name.

---

## Official documentation
- DWD ICON-D2 model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_d2/icon_d2_node.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD Open Data Server root: https://opendata.dwd.de/weather/nwp/
