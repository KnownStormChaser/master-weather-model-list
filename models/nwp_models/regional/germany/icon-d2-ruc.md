# ICON-D2-RUC (DWD Rapid Update Cycle convection-permitting model)

## What this model is
ICON-D2-RUC is DWD's **rapid-update-cycle, convection-permitting** regional NWP configuration of ICON, designed for very short-range nowcasting-to-forecasting of rapidly evolving severe weather such as thunderstorms, supercells, squall lines, hail events, and flash floods.

It runs the same ICON limited-area model core as the standard ICON-D2 over the same domain and at the same 2.2 km horizontal resolution, but with two key operational differences: **hourly initialisation** (24× per day) and a **shorter +14 hour forecast range**. DWD describes ICON-D2-RUC as a "fraternal twin" of ICON-D2 — same model, different operational use case. Each forecast is available roughly 40 minutes after model start, allowing forecasters and aviation users to react to new observations almost in real time.

ICON-D2-RUC also uses the more advanced two-moment bulk microphysics scheme of Seifert and Beheng, which produces more realistic radar reflectivities than the single-moment scheme used in standard ICON-D2 — particularly at strong convective cores. The system grew out of DWD's SINFONY (Seamless INtegrated FOrecastiNg sYstem) project, which developed a seamless prediction framework integrating nowcasting techniques with NWP at the convective scale.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country:** Germany

---

## What area it covers
- **Coverage:** Germany, Switzerland, Austria, Benelux countries, and parts of neighbouring countries (same domain as ICON-D2)

---

## Basic details
- **Model type:** Regional convection-permitting deterministic NWP, rapid update cycle
- **Underlying core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Horizontal resolution:** 2.2 km (same as ICON-D2)
- **Vertical levels:** 65
- **Forecast length:** +14 hours
- **Update frequency:** Hourly (24× daily)
- **Availability latency:** Forecast data available ~40 minutes after model start
- **Cloud microphysics:** Two-moment bulk scheme (Seifert and Beheng), more advanced than the single-moment scheme used in standard ICON-D2
- **Operational since:** 12 July 2024

---

## Data assimilation
ICON-D2-RUC runs an **hourly KENDA-LETKF data assimilation cycle** — substantially more frequent than the 3-hourly cycle used by standard ICON-D2. The hourly cycle is one of the system's defining operational features: it allows the model to absorb new radar volume scans, surface observations, and other near-real-time data each hour, forcing the forecast to "return to reality" much more often than coarser-cadence systems can.

The DA cycle restarts daily at 03 UTC from the standard ICON-D2 cycle (rather than running continuous indefinite cycles). Assimilated observations include:
- 3D radar reflectivity volumes from the German radar network
- Radial wind from the German radar network
- Conventional surface and upper-air observations
- Aircraft, radiosonde, and satellite observations inherited from the ICON-D2 DA stream
- Latent Heat Nudging (LHN) using radar-derived precipitation fields

The combination of hourly DA, two-moment microphysics, and rapid-update forecast cycling targets the forecast skill window between 0–6 hours, where pure nowcasting extrapolation begins to fail and traditional 3-hourly NWP cycles haven't yet refreshed.

---

## What it provides
- Full set of surface and upper-air fields at 2.2 km resolution (temperature, wind, gust, humidity, pressure, precipitation by type, cloud, radiation, etc.)
- Convection-allowing severe weather products: thunderstorm probability, heavy precipitation indicators, hail-relevant fields, wind gust forecasts
- Lightning Potential Index (LPI) and related convective-weather diagnostics
- Simulated radar reflectivity at higher fidelity than ICON-D2 (due to two-moment microphysics)
- Aviation-relevant fields with emphasis on rapidly developing convective hazards

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data format:** GRIB2
- **Primary access:** DWD Open Data Server
  - ICON-D2-RUC: https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-ruc/
- **Distribution path note:** ICON-D2-RUC is distributed under `/weather/nwp/v1/m/` rather than alongside the main ICON, ICON-EU, and ICON-D2 directories at `/weather/nwp/`. The `v1/m/` path appears to be a separate distribution tier introduced in 2025 and also hosts ICON-D2-RUC-EPS and the ICON-ART family.
- **Licence:** DWD Open Data licence (CC-BY 4.0-compatible; attribution required)
- **Retention note:** DWD retains GRIB2 files on the open data server for a short period only (typically 24 hours).

---

## Notes
- ICON-D2-RUC is the rapid-update sibling of the standard [ICON-D2](./icon-d2.md) — same model core, same domain, same 2.2 km grid, but hourly cycling and 14-hour forecast range. The two systems run in parallel in operations, complementing rather than replacing each other.
- The hourly update cadence is operationally rare. Internationally, only a small number of weather services run convection-permitting models in true rapid-update mode — peers include NOAA's HRRR (also hourly, 18–48 h depending on cycle) and the experimental MeteoSwiss rapid-update configurations. DWD's combination of hourly DA + two-moment microphysics + 40-minute availability sits in a thin field globally.
- The two-moment microphysics scheme is a meaningful difference from standard ICON-D2 for radar-related applications. Users comparing simulated reflectivity between the two systems should expect ICON-D2-RUC to show more realistic values at strong convective cores.
- ICON-D2-RUC is the operational descendant of work done within DWD's SINFONY project, which targeted seamless prediction across the nowcasting-to-NWP transition at 0–12 hours.
- The ensemble counterpart is [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md).

---

## Recent version history

### 5 February 2025 — model version icon-2024.10-dwd-2.1 (effective with the 08 UTC RUC run)
Shared with the standard ICON-D2 upgrade (the precipitation-intensity retuning does **not** apply to RUC, which already uses two-moment microphysics):
- **TERRA_URB urban canopy scheme activated.**
- **Assimilation of meteorological ICOS tower data** (9 towers over Germany).
- **Retuned visibility diagnostic** (corrects nighttime fog underestimation).

---

## Official documentation
- DWD ICON model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_d2/icon_d2_node.html
- DWD ICON Database Reference: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD Open Data Server v1/m subtree: https://opendata.dwd.de/weather/nwp/v1/m/
- ICON-D2-RUC overview (PILOT-HUB): https://pilot-hub.com/en/2-13/
