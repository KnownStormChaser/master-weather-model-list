# ALADIN (CHMI – Czech Republic)

## What this model is
ALADIN (CHMI) is a **regional limited-area numerical weather prediction (NWP) model** operated by the Czech Hydrometeorological Institute.

It is based on the **ALADIN modeling system** and is used for short-range forecasting over Czechia and surrounding Central European regions, with particular strength in complex terrain and mesoscale weather phenomena.

---

## Who runs it
- **Organization:** Czech Hydrometeorological Institute (ČHMÚ)
- **Country / region:** Czech Republic

---

## What area it covers
- **Coverage:** Czechia and surrounding Central Europe
- **Model domain:** Central Europe, with Czech Republic near the center

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system:** ALADIN
- **Horizontal resolution:** **2.3 km**
- **Forecast length:** Up to **72 hours**
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 1 hour

---

## Model characteristics
- Non-hydrostatic configuration with detailed orography
- Advanced radiation scheme with explicit calculation of:
  - shortwave radiation
  - longwave radiation
  - **mean radiant temperature (Tmrt)**

Since 2019, the model includes **operational calculation of the Universal Thermal Climate Index (UTCI)**, supporting biometeorological forecasting, heat-health warnings, and climate services.

---

## Data assimilation and coupling
- **Coupling model:** ARPEGE (ECMWF/ARPEGE lineage)
- Continuous development within the **RC LACE** framework
- Radiation and surface diagnostics fully integrated into the forecast cycle

---

## What it provides
Deterministic forecasts of:
- temperature, humidity, and wind
- surface and mean sea-level pressure
- hourly precipitation
- cloud cover and radiation fluxes
- derived biometeorological indices (including UTCI)

Outputs are used operationally for **weather forecasting, biometeorology, warnings, and public services** in Czechia.

---

## Data availability
- **Is the data free?** Yes  
- **Is the data downloadable?** Yes  
- **Data formats:** GRIB2  
- **Official download location:**  
  https://opendata.chmi.cz/meteorology/weather/nwp_aladin/

---

## Notes
The current ALADIN (CHMI) configuration has been operational since **February 2019**, replacing the earlier 4.6 km version with improved resolution, orography, and radiation diagnostics.

CHMI’s implementation is notable for its **operational biometeorological outputs**, which are uncommon among national ALADIN systems.

---

## Official documentation
- Novák, M. (2021): *UTCI as the NWP model ALADIN (CHMI) output – first experiences*, Geographia Polonica
