# CMA-GFS (formerly GRAPES-GFS)

## What this model is
CMA-GFS is the operational global deterministic numerical weather prediction system developed and run by the China Meteorological Administration. It is the core global medium-range forecasting system of Chinese operational meteorology.

The model was previously known as **GRAPES-GFS** (Global/Regional Assimilation and Prediction Enhanced System — Global Forecasting System). Following the V4.0 upgrade in February 2021, CMA rebranded the system as **CMA-GFS**. The legacy "GRAPES" name still appears in directory paths on CMA's public distribution endpoint and in older literature, but the current official name is CMA-GFS.

---

## Who runs it
- **Organization:** China Meteorological Administration (CMA) / CMA Earth System Modeling and Prediction Centre (CEMC)
- **Country / region:** China

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Deterministic global NWP
- **Model system / core:** CMA-GFS (semi-implicit, semi-Lagrangian dynamical core with predictor–corrector time integration)
- **Horizontal resolution:** ~0.125° (~15 km)
- **Forecast length:**
  - 240 hours (10 days) for 00 and 12 UTC cycles
  - 120 hours (5 days) for 06 and 18 UTC cycles
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**
  - 6-hourly for 00 and 12 UTC cycles
  - 3-hourly for 06 and 18 UTC cycles

---

## Model characteristics
The CMA-GFS V4.0 physics package includes:
- **Cumulus convection:** Simplified Arakawa–Schubert (SAS) scheme
- **Cloud microphysics:** Double-moment scheme
- **Radiation:** RRTMG longwave and shortwave
- **Land surface:** Common Land Model (CoLM)
- **Boundary layer:** New Medium Range Forecast (NMRF) scheme

---

## Data assimilation
- **Satellite data assimilation:** ~80% of assimilated observations are satellite-derived, including from Chinese FY (Fengyun) satellites
- **Methane oxidation scheme** introduced in V3.1

---

## What it provides
Deterministic global forecasts of:
- Temperature, humidity, and wind (including near-surface wind at 10, 30, 50, 70, 100, 120, 140, 160, 180, and 200 m above ground)
- Surface and mean sea level pressure
- Precipitation
- Cloud and hydrometeor fields
- 30 atmospheric pressure levels for upper-air variables

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/

Data is organized into four cycle directories:
- `t0000/` — 00 UTC cycle, forecast hours 0–240 at 6-hour intervals
- `t0600/` — 06 UTC cycle, forecast hours 0–120 at 3-hour intervals
- `t1200/` — 12 UTC cycle, forecast hours 0–240 at 6-hour intervals
- `t1800/` — 18 UTC cycle, forecast hours 0–120 at 3-hour intervals

The `gmf_gra` path component retains the legacy "GRAPES" naming despite the V4.0 rebranding.

---

## Version history

### CMA-GFS V4.0 — operational February 2021
- Horizontal resolution increased from 0.25° to 0.125°
- Rebranded from GRAPES-GFS to CMA-GFS
- Significant improvements to precipitation forecasting
- Satellite data now accounts for approximately 80% of assimilated observations, including from Chinese FY satellites

### GRAPES-GFS V3.1 — operational April 2021 (physics improvements)
- Methane oxidation scheme added
- Updated land surface model
- Improved moist physical processes

### GRAPES-GFS dynamical core upgrade — operational 2020
- Semi-implicit semi-Lagrangian (SISL) scheme extended to predictor–corrector formulation
- 3D reference profile replaces isothermal reference profile
- Hybrid terrain-following vertical coordinate introduced
- Time integration now second-order accurate; time step extended by 50%; ~30% efficiency improvement

---

## Notes
- CMA-GFS is the deterministic counterpart to CMA's global ensemble system (CMA-GEPS), which also uses the GRAPES dynamical core and was described in earlier literature as GRAPES-GEPS.
- Distribution is via CMA's WIS (WMO Information System) public endpoint operated by the World Meteorological Centre Beijing.
- The `gmf_gra` directory path on the WIS server preserves the legacy GRAPES naming; users encountering documentation referring to "GRAPES-GFS" or "GFS GRAPES" are working with the same system now officially called CMA-GFS.

---

## Official documentation
- World Meteorological Centre Beijing: http://www.wmc-bj.net/
- CMA-GFS V4.0 operational announcement (WMC Beijing): http://www.wmc-bj.net/publish/cms/view/wmcbj_a6684add56eb4a92973b7680920af593.html
- CMA Earth System Modeling and Prediction Centre: https://www.cma.gov.cn/
