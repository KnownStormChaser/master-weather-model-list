# ICON-EU (ICON Europe)

## What this model is
ICON-EU is DWD's regional, convection-parameterized deterministic NWP configuration of ICON, covering Europe at higher resolution than the global run.

It is not a separately initialized limited-area model but the **two-way coupled European nest** embedded within the global [ICON](../../global/germany/icon-global.md) integration — boundary conditions and feedbacks are exchanged with the global domain every model time step. Its primary roles are higher-resolution deterministic guidance over Europe and supplying lateral boundary conditions to DWD's convection-permitting [ICON-D2](./icon-d2.md) family.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany / Europe

---

## What area it covers
- **Coverage:** Europe (regional nest of the global ICON integration)
- **Domain bounds (approx.):** 23.5°W – 62.5°E, 29.5°N – 70.5°N

---

## Basic details
- **Model type:** Regional deterministic NWP (two-way coupled nest)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic)
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** No (deep convection parameterized at ~6.5 km)
- **Horizontal resolution:** ~6.5 km (R3B08 triangular grid)
- **Public output grids:** Native triangular grid (GRIB2); regular latitude–longitude grids interpolated from native for many element packages
- **Vertical levels:** 74 (increased from 60 in the November 2022 upgrade)
- **Model top:** ~22.8 km
- **Forecast length:** 120 hours (5 days) for the main synoptic cycles (00, 06, 12, 18 UTC); 51 hours for the intermediate cycles (03, 09, 15, 21 UTC)
- **Update frequency / cycles:** 8× daily — 4 main synoptic cycles (00, 06, 12, 18 UTC) plus 4 intermediate off-synoptic cycles (03, 09, 15, 21 UTC), matching the global ICON integration
- **Temporal output resolution:** Hourly to +78 h, then 3-hourly (matching the global ICON integration)

---

## Data assimilation
ICON-EU does not run an independent analysis. As the two-way European nest of the global ICON integration, it inherits initial conditions from DWD's **hybrid LETKF + EnVar** data assimilation system, whose LETKF ensemble component runs a 20 km nest over Europe (see the [ICON Global entry](../../global/germany/icon-global.md#data-assimilation) for the full DA description).

---

## Initial and boundary conditions
- **Coupling:** Two-way nested within the global ICON integration, rather than one-way driven by an external global model. The global domain and the European nest exchange boundary information and feedbacks every model time step.
- **Downstream use:** ICON-EU in turn supplies the lateral boundary conditions for the one-way-driven [ICON-D2](./icon-d2.md) and [ICON-D2-RUC](./icon-d2-ruc.md) convection-permitting systems.

---

## What it provides
Deterministic forecasts over Europe at higher resolution than ICON Global:
- temperature, wind, precipitation, pressure, humidity, cloud and hydrometeor fields
- lateral boundary conditions / driving data for the convection-permitting ICON-D2 family

---

## Data availability
- **Is the data free?** Yes
- **License:** CC BY 4.0 (DWD Open Data licence; attribution required)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (native triangular grid; regular lat-lon grids also distributed for many element packages)
- **Official download location:**  
  https://opendata.dwd.de/weather/nwp/icon-eu/
- **Retention note:** DWD retains GRIB2 files on the open data server for a short period only (typically 24 hours). Users needing longer-term archives must set up their own retention.

DWD provides CDO grid description and weight files for interpolating native triangular output to custom regular grids:
- https://opendata.dwd.de/weather/lib/cdo/

---

## Notes
- ICON-EU and the global [ICON](../../global/germany/icon-global.md) are not separate models — ICON-EU is the higher-resolution European nest of a single tightly coupled two-way integration. This is structurally different from regional models elsewhere (e.g., ARPEGE/AROME at Météo-France) where the limited-area model runs as a separate process driven by one-way lateral boundary conditions.
- The ensemble counterpart is [ICON-EU-EPS](../../../ensemble_models/regional/de/icon-eu-eps.md), distributed as the European-nest portion of [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md).
- The ICON model code has been open source under a permissive licence since January 2024 (repository: https://gitlab.dkrz.de/icon/icon-model).
- ICON-EU per-cycle horizons (120 h for 00/06/12/18 UTC; 51 h for 03/09/15/21 UTC) are taken from DWD's EWGLAM 2024, ICCARUS 2025, and EWGLAM 2025 operational-chain posters, which agree. Note the EU nest caps at 120 h even for the 00/12 cycles, whereas the global ICON integration runs those to 180 h.
---

## Recent version history

### 23 November 2022 — vertical resolution upgrade
European nest vertical levels increased from 60 to 74 as part of the broader ICON November 2022 upgrade. The deterministic ICON-EU horizontal resolution remained at ~6.5 km (the simultaneous horizontal-resolution increase applied to ICON-EPS, not the deterministic nest).

### June 2015 — operational launch
ICON-EU introduced as the European nest of the ICON system, replacing the legacy COSMO-EU regional model. (Global ICON had replaced GME five months earlier, in January 2015.)

---

## Official documentation
- DWD ICON model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD NWP forecast data overview: https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD Open Data Server root: https://opendata.dwd.de/weather/nwp/
- DWD ICON open source repository: https://gitlab.dkrz.de/icon/icon-model
