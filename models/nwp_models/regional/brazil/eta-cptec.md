# ETA (CPTEC/INPE Regional Model)

## What this model is
The ETA model is a regional numerical weather prediction (NWP) system operated by CPTEC/INPE to provide short- to medium-range weather forecasts over Brazil and surrounding regions.

The model is based on the ETA dynamical core and is run in several operational configurations that differ by domain, horizontal resolution, and update frequency.

---

## Who runs it
- **Organization:**  CPTEC/INPE (Center for Weather Forecasting and Climate Studies / National Institute for Space Research)
- **Country / region:** Brazil

---

## What area it covers
- **Coverage:** Regional domains over Brazil and adjacent areas
- **Domain strategy:**  
  Multiple fixed domains optimized for national and sub-regional forecasting

---

## Model configurations

CPTEC operates the ETA model in the following publicly available configurations:

### ams_40km
- **Horizontal resolution:** ~40 km
- **Coverage:** Large South America / Brazil domain
- **Update frequency:** 1× daily
- **Forecast range:** Short- to medium-range

### ams_08km
- **Horizontal resolution:** ~8 km
- **Coverage:** Brazil and surrounding regions
- **Update frequency:** **2× daily**
- **Forecast range:** Short-range to medium-range

### ons_40km
- **Horizontal resolution:** ~40 km
- **Coverage:** Regional Brazil-focused domain
- **Update frequency:** 1× daily
- **Forecast range:** Short- to medium-range

### rjsp_01km
- **Horizontal resolution:** ~1 km
- **Coverage:** Rio de Janeiro / São Paulo region
- **Update frequency:** 1× daily
- **Forecast range:** Short-range, high-resolution local forecasting

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system:** ETA
- **Forecast lead time:**  
  Varies by configuration
- **Temporal output resolution:**  
  Model-dependent

---

## What it provides
Deterministic regional forecasts of core atmospheric variables, including:
- Near-surface temperature, wind, and humidity
- Atmospheric pressure
- Precipitation
- Regional circulation patterns

The ETA model complements CPTEC’s global guidance by resolving mesoscale and local-scale weather features.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB
- **Official download location:**  
  https://dataserver.cptec.inpe.br/dataserver_modelos/eta/

(Subdirectories correspond to individual ETA configurations.)

---

## Notes
- Update frequency differs by configuration; **ams_08km is the only configuration currently updated twice daily**.
- Other ETA configurations are updated once per day.
- Model domains and resolutions may evolve as part of CPTEC’s operational development.

---

## Official information
- CPTEC/INPE model data server  
  https://dataserver.cptec.inpe.br/
