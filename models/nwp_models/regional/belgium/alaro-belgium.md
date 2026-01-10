# ALARO-0 Belgium (RMI)

## What this model is
ALARO-0 Belgium is a **regional limited-area numerical weather prediction (NWP) model** operated by the Royal Meteorological Institute of Belgium.

It is based on the **ALADIN system** using the **ALARO-0 physics package**, which is specifically designed for so-called *gray-zone* resolutions between mesoscale and convection-permitting scales.

---

## Who runs it
- **Organization:** Royal Meteorological Institute of Belgium (RMI / KMI)
- **Country / region:** Belgium

---

## What area it covers
- **Coverage:** Belgium and surrounding Western European regions
- **Model domain:** Western Europe, centered on Belgium

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system:** ALADIN / ALARO-0
- **Horizontal resolution:** ~4 km
- **Vertical levels:** 40  
- **Forecast length:** Up to **48 hours**
- **Update frequency:** 2× daily (00, 12 UTC)
- **Temporal output resolution:** 1 hour

---

## Model characteristics
- **Physics package:** ALARO-0
- **Convection:** Modular Multiscale Microphysics and Transport (**3MT**) scheme
- **Radiation:** ACRANEB
- **Turbulence:** Prognostic TKE scheme
- Optimized for **gray-zone convection**, intense precipitation, and frontal systems

ALARO-0 has demonstrated improved performance over classical ALADIN configurations for **extreme precipitation**, particularly at 4 km resolution.

---

## Data assimilation and coupling
- **Coupling model:** ARPEGE
- Developed within the **ALADIN consortium** and **RC LACE**
- Operational configuration at RMI since the late 2000s, with continuous upgrades

---

## Data availability
- **Is the data free?** Yes  
- **Is the data downloadable?** Yes  
- **Data formats:** GRIB2  
- **Public dataset name:** `alaro_40l`  
- **Official download location:**  
  https://opendata.meteo.be/ftp/forecasts/alaro_40l/

*(Note: `40l` refers to the number of vertical levels, not horizontal resolution.)*

---

## Notes
RMI also operates **AROME** and several **ensemble systems** (e.g. GLAMEPS, RMI-EPS), but `alaro_40l` is currently the **only openly downloadable deterministic NWP model** provided by RMI.

---

## Official documentation
- https://www.meteo.be/en/research-rmi/scope-of-research/numerical-model-alaro/numerieke-weersvoorspellingen  
- De Troch et al. (2013): *Multiscale Performance of the ALARO-0 Model for Simulating Extreme Summer Precipitation in Belgium*
