# WAVEWATCH III – MET Norway (Nordic Seas)

## What this model is
MET Norway’s WAVEWATCH III (WW3) Nordic Seas system is an **operational third-generation spectral wave forecast model** used to predict sea-state conditions in the North Atlantic, Nordic Seas, and adjacent Arctic waters.

The model provides a statistical representation of the sea state, including wind sea and swell components, and is optimized for high-latitude and high-impact marine forecasting.

---

## Who runs it
- **Organization:** Norwegian Meteorological Institute (MET Norway)
- **Country / region:** Norway

---

## What area it covers
- **Coverage:**  
  - Nordic Seas  
  - Norwegian Sea  
  - Barents Sea  
  - Northern North Atlantic  

This system forms the regional backbone of MET Norway’s operational wave forecasting.

---

## Basic details
- **Model type:** Spectral wave model (third generation)
- **Model system:** WAVEWATCH III
- **Horizontal resolution:** ~4 km
- **Spectral resolution:** 36 frequencies × 36 directions
- **Forecast length:** **66 hours**
- **Update frequency:** 4× daily
- **Temporal output resolution:** 1 hour

---

## Model forcing and boundaries
- **Wind forcing:**
  - ECMWF-IFS (open ocean)
  - AROME-MEPS (2.5 km) over Norwegian coastal regions
- **Sea-ice concentration:** OSI-SAF
- **Boundary wave spectra:** ECMWF ECWAM

---

## What it provides
Deterministic forecasts of:
- significant wave height
- peak and mean wave period
- mean wave direction
- wind sea characteristics
- swell components
- combined sea state parameters

All parameters represent statistically averaged sea-state conditions.

---

## Data availability
- **Is the data free?** Yes  
- **Is the data downloadable?** Yes  
- **Data formats:** NetCDF  
- **Access method:** THREDDS  
- **Official download location:**  
  https://thredds.met.no/thredds/fou-hi/ww3_4km.html

---

## Notes
MET Norway also operates **WAM-based wave models** (Pan-Arctic and high-resolution coastal domains) and long-term hindcasts such as **NORA10** and **NORA3**. These are distinct systems and should be documented separately.

The WW3 Nordic Seas configuration is primarily intended for **short-range marine forecasting, offshore operations, and Arctic navigation**.

---

## Official documentation
- https://ocean.met.no/waves
