# ICON-D2-EPS (ICON-D2 Regional Convection-Allowing Ensemble)

## What this model is
ICON-D2-EPS is DWD's operational convection-allowing regional ensemble prediction system for Germany and surrounding central European countries. It is the ensemble counterpart of DWD's deterministic ICON-D2 model, with the same 2.2 km horizontal resolution — fine enough to resolve deep convective processes explicitly without parameterization.

ICON-D2-EPS replaced the earlier COSMO-D2-EPS on 10 February 2021, when DWD migrated all aviation weather products from the COSMO model to ICON-D2. The system is specifically designed to capture probabilistic forecasts of hazardous weather events with strong fine-scale structure: thunderstorms, squall lines, heavy convective precipitation, flash-flood-inducing storms, fine-scale topographic effects (Föhn winds, downslope winds, ground fog).

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country:** Germany

---

## What area it covers
- **Coverage:** Germany, Benelux (Belgium, the Netherlands, Luxembourg), Switzerland, Austria, and parts of neighbouring countries

---

## Basic details
- **Model type:** Regional convection-allowing ensemble prediction system
- **Underlying core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Number of ensemble members:** 20
- **Horizontal resolution:** 2.2 km (same as the deterministic ICON-D2)
- **Vertical levels:** 65 levels
- **Forecast length:** Up to +48 hours (matching the deterministic ICON-D2)
- **Update frequency:** 8× daily (matching ICON-D2: 00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:** Hourly

---

## How the ensemble is generated
ICON-D2-EPS member generation perturbs multiple components:

- **Lateral boundary conditions:** Derived from forecasts of various global models. Historically, boundaries were supplied by members 1–20 of the global ICON-EPS; DWD has since diversified boundary sources to include other global models for additional spread.
- **Initial state:** From an ensemble data assimilation cycle
- **Soil moisture:** Stochastically perturbed at initialization
- **Model physics:** Randomized physics perturbations across members

Because ICON-D2 is convection-allowing, it resolves deep convective features that are parameterized at coarser scales. This means error growth at small spatial and temporal scales is faster than in coarser systems, making probabilistic forecasts especially valuable for short-range forecasting of severe weather events.

---

## What it provides
- Full set of surface and upper-air fields at 2.2 km resolution (temperature, wind, gust, humidity, pressure, precipitation by type, cloud, radiation, etc.)
- Convection-allowing probabilistic products: thunderstorm probability, heavy precipitation probability, hail probability, wind gust exceedance probability
- Lightning Potential Index (LPI) and related convective-weather parameters
- Aviation-relevant fields (migrated from COSMO-D2 in February 2021)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data format:** GRIB2
- **Primary access:** DWD Open Data Server
  - ICON-D2-EPS: https://opendata.dwd.de/weather/nwp/icon-d2-eps/grib/
- **Grids available:** Native icosahedral grid (triangular); regular lat-lon grid also distributed for many element packages
- **Licence:** DWD Open Data licence (CC-BY 4.0-compatible; attribution required)
- **Retention note:** DWD retains GRIB2 files on the open data server for a short period only (typically 24 hours).

---

## Notes
- ICON-D2-EPS is the ensemble counterpart of the deterministic ICON-D2 model — see the ICON-D2 entry for the shared model core and operational details.
- The system was renamed from COSMO-D2-EPS to ICON-D2-EPS on 10 February 2021 as part of DWD's migration from the COSMO model family to the ICON model family. Older references (and older DWD documentation pages) may still use the COSMO-D2-EPS name.
- At 2.2 km resolution with 20 members, ICON-D2-EPS is one of the higher-resolution operational convection-allowing ensembles in operational use. Comparable systems include MeteoSwiss ICON-CH1-EPS (1 km, 11 members) and Météo-France AROME-EPS.

---

## Official documentation
- DWD ICON-D2 model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_d2/icon_d2_node.html
- DWD ensemble prediction overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/04_ensemble_methods/ensemble_prediction/ensemble_prediction_node.html
- DWD Open Data Server root: https://opendata.dwd.de/weather/nwp/
- COSMO-D2 → ICON-D2 transition notice (10 February 2021): documented in DWD change notices
