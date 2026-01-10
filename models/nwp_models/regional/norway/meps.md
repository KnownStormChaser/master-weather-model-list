# MEPS (MetCoOp Ensemble Prediction System)

## What this model is
MEPS (MetCoOp Ensemble Prediction System) is a **high-resolution, convection-permitting regional ensemble numerical weather prediction (NWP) system**.

It is designed to provide **short-range probabilistic forecasts** and quantify forecast uncertainty for high-impact weather such as precipitation, wind, and convection.

---

## Who runs it
- **Organization:** MetCoOp collaboration  
  (primarily MET Norway and SMHI, with participation from FMI and DMI)
- **Country / region:** Nordic countries

---

## What area it covers
- **Coverage:** Norway, Sweden, Denmark, Finland, and surrounding marine areas  
  (including parts of the North Sea and Baltic Sea)

---

## Basic details
- **Model type:** Regional ensemble (convection-permitting)
- **Model system:** HARMONIE-AROME
- **Horizontal resolution:** ~2.5 km
- **Vertical levels:** 65
- **Ensemble size:** 10 perturbed members + 1 control (11 total)
- **Forecast length:** Up to 66 hours
- **Update frequency:** 8× daily (every 3 hours)

---

## What it provides
Probabilistic forecasts of:
- temperature
- wind
- precipitation
- pressure
- humidity
- cloud and hydrometeor fields

Ensemble output enables:
- probability forecasts
- ensemble means and spread
- uncertainty assessment for short-range weather events

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, NetCDF
- **Official download location:**  
  https://thredds.met.no/

---

## Notes
MEPS is produced under the **MetCoOp** framework, a long-standing Nordic collaboration on operational numerical weather prediction.

It is the primary **short-range ensemble forecasting system** used operationally across the Nordic meteorological services.

---

## Official documentation
- MEPS dataset documentation:  
  https://github.com/metno/NWPdocs/wiki/MEPS-dataset
- MetCoOp project overview:  
  https://www.met.no/en/projects/metcoop
