# NAVGEM (Navy Global Environmental Model)

## What this model is
The Navy Global Environmental Model (NAVGEM) is a global numerical weather prediction model developed and operated by the U.S. Navy for worldwide atmospheric forecasting.

NAVGEM is designed to meet naval and maritime forecasting requirements, including global weather prediction, support for coupled atmosphere–ocean–ice systems, and provision of initial and boundary conditions for downstream regional and ocean models. It is the successor to the Navy Operational Global Atmospheric Prediction System (NOGAPS).

---

## Who runs it
- **Organization:** Fleet Numerical Meteorology and Oceanography Center (FNMOC) / Naval Research Laboratory
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Deterministic global NWP
- **Model system / core:**  
  Semi-Lagrangian / semi-implicit (SL/SI) hydrostatic dynamical core
- **Horizontal resolution:**  
  ~18–25 km class (spectral T681-equivalent; Gaussian grid)
- **Vertical levels:** 60
- **Model top:** ~0.04 hPa (~70 km)
- **Forecast length:**  
  Up to ~16 days
- **Update frequency / cycles:**  
  4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**  
  Typically 3-hourly

---

## What it provides
Deterministic global forecasts of:
- Atmospheric temperature, wind, humidity, and pressure
- Surface fluxes important for ocean and wave modeling
- Tropical cyclone environment and track guidance
- Upper-atmospheric fields extending into the mesosphere

NAVGEM output is widely used as:
- Atmospheric forcing for ocean, sea-ice, and wave models
- Initial and boundary conditions for limited-area models
- The atmospheric component of the Navy Earth System Prediction Capability (ESPC) 

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/fnmoc/prod/

---

## Notes
- NAVGEM uses a semi-Lagrangian / semi-implicit formulation to allow higher global resolution without prohibitively small time steps, a key limitation of its predecessor NOGAPS.
- The model is developed primarily at the Naval Research Laboratory and transitioned operationally at FNMOC.
- NAVGEM serves as the atmospheric core of the Navy Earth System Prediction Capability (ESPC), where it is coupled with HYCOM (ocean), CICE (sea ice), and WAVEWATCH III (waves). 
- While NAVGEM data are publicly available, the model is optimized for naval and maritime applications rather than civilian public forecasting.

---

## Official documentation
- https://www.metoc.navy.mil/fnmoc/fnmoc.html
- Hogan et al. (2014), *The Navy Global Environmental Model*, Oceanography
