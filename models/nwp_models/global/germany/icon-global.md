# ICON (Icosahedral Nonhydrostatic) — Global

## What this model is
ICON is the operational global deterministic numerical weather prediction model run by the German Weather Service (Deutscher Wetterdienst, DWD).

It uses a non-hydrostatic dynamical core on a triangular icosahedral grid, with a tightly coupled two-way nest over Europe (ICON-EU) running at higher resolution within the same forecast integration. ICON predicts atmospheric conditions worldwide — temperature, wind, precipitation, pressure, humidity, clouds, and hydrometeor fields — out to 7.5 days for the main cycles.

ICON replaced DWD's previous global model (GME) on 20 January 2015. The model code is jointly developed by DWD and the Max Planck Institute for Meteorology (MPI-M), and has been open source under a permissive licence since January 2024.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD)
- **Country / region:** Germany
- **Code development:** Joint between DWD and the Max Planck Institute for Meteorology

---

## What area it covers
- **Coverage:** Global
- **Regional nest:** A two-way coupled nest covers Europe at ~6.5 km horizontal resolution; this is documented separately as [ICON-EU](./icon-eu.md). Nest boundary conditions and feedbacks are exchanged every model time step.

---

## Basic details
- **Model type:** Global deterministic NWP
- **Model system / core:** ICON (Icosahedral Nonhydrostatic)
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** No (deep convection is parameterized at ~13 km resolution)
- **Native horizontal grid:** R3B07 triangular grid with 2,949,120 cells, ~13 km effective mesh size
- **Public output grids:**
  - Native triangular grid (GRIB2)
  - Regular latitude–longitude at 0.25° (~28 km) and 0.125° (~14 km), interpolated from native
- **Vertical levels:** 120 (since November 2022; 90 levels prior)
- **Vertical coordinate:** Smooth-Level Vertical (SLEVE) terrain-following hybrid coordinate. Levels are terrain-following near the surface, transitioning to flat (purely height-based) levels above 16 km.
- **Model top:** 75 km
- **Forecast length:**
  - 180 hours (7.5 days) for 00 and 12 UTC cycles
  - 120 hours (5 days) for 06 and 18 UTC cycles
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly to +78 h, then 3-hourly to +180 h

---

## Data assimilation
ICON uses a **hybrid LETKF + EnVar** data assimilation system, distinctive among national centres in combining ensemble Kalman filtering with a variational analysis driven by ensemble-derived covariances:

- **Ensemble component:** A 40-member Local Ensemble Transform Kalman Filter (LETKF) ensemble runs at 40 km horizontal resolution globally, with a 20 km nest over Europe. This is the same ensemble used to produce [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md).
- **Deterministic analysis:** An Ensemble Variational (EnVar) scheme based on Buehner's formulation, where the background error covariances are dynamically derived from the LETKF ensemble. The EnVar produces the analysis that initializes the deterministic 13 km global / 6.5 km European nest forecasts.
- **Cycle structure:** A 3-hour assimilation window centred on the analysis time is used, with the previous 3-hour forecast as the first guess.

The hybrid LETKF + EnVar system has been operational at DWD since January 2015 and provides initial conditions for both the deterministic ICON forecasts and ICON-EPS.

---

## What it provides
Deterministic global forecasts of:
- Temperature (surface and upper-air)
- Wind components (10 m and upper-air)
- Specific and relative humidity
- Surface and mean sea level pressure
- Total precipitation, precipitation type, and convective precipitation
- Cloud cover (total, high, mid, low) and hydrometeor fields (cloud water, ice, rain, snow, graupel)
- Surface fluxes, radiation components, and boundary-layer diagnostics
- Aviation-relevant fields including icing indices and lightning potential

ICON output also serves as boundary forcing for downstream regional configurations including ICON-D2 (when boundaries are not provided by ICON-EU directly) and several non-DWD ICON-LAM applications.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** CC BY 4.0 (DWD Open Data licence; attribution required)
- **Data formats:** GRIB2
- **Grids available on Open Data:**
  - Native icosahedral (triangular) grid
  - Regular lat/lon at 0.25° and 0.125°
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/icon/
- **Retention note:** DWD retains GRIB2 files on the open data server for a short period only (typically 24 hours). Users needing longer-term archives must set up their own retention.

DWD provides CDO grid description files and weight files for users wanting to interpolate native triangular output to custom regular grids:
- https://opendata.dwd.de/weather/lib/cdo/

---

## Open source
The ICON modelling system became open source in **January 2024** under a permissive licence (BSD-3-Clause-style), with the source code, documentation, and runtime tooling available via the joint DWD/MPI-M GitLab. This makes ICON one of the few major operational global NWP systems with publicly available source code, alongside the open-sourcing of components by ECMWF (Anemoi/AIFS, ecCodes) and others.

---

## Notes
- ICON Global and [ICON-EU](./icon-eu.md) are not separate models — they are the global parent and European nest of a single tightly-coupled two-way nested integration. This is structurally different from regional models elsewhere (e.g., ARPEGE/AROME at Météo-France) where the limited-area model is run as a separate process driven by the global model's lateral boundary conditions.
- The November 2022 vertical resolution upgrade from 90 to 120 levels (with the model top remaining at 75 km) targeted improvements in the stratosphere; the upgrade also brought ICON-EPS from 40 km to 26 km horizontal resolution and increased ICON-EU vertical levels from 60 to 74.
- The ensemble counterpart is [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md). The European nest portion of ICON-EPS is distributed separately as [ICON-EU-EPS](../../../ensemble_models/regional/de/icon-eu-eps.md).
- DWD's regional convection-permitting model [ICON-D2](../regional/germany/icon-d2.md) and its rapid-update variant [ICON-D2-RUC](../regional/germany/icon-d2-ruc.md) are separate limited-area systems driven by ICON-EU boundaries.
- DWD has also developed an experimental AI-based forecasting system, **AICON**, trained on the ICON reanalysis (ICON-DREAM). AICON is in development and is not part of the operational ICON suite as of early 2026.

---

## Official documentation
- DWD ICON model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD NWP forecast data overview: https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD ICON Tutorial 2025: https://www.dwd.de/EN/ourservices/nwp_icon_tutorial/pdf_volume/icon_tutorial2025_en.pdf
- DWD model upgrade timeline: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD Open Data root: https://opendata.dwd.de/weather/nwp/
- DWD ICON open source repository: https://gitlab.dkrz.de/icon/icon-model

### Key references
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Reinert, D., Prill, F., Frank, H., Denhard, M., Baldauf, M., Schraff, C., Gebhardt, C., Marsigli, C., and Zängl, G. (2024). *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.* Version 2.x.
