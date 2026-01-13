# OpenWRF

## What this model is
OpenWRF is an independently operated high-resolution regional numerical weather prediction (NWP) system based on the Weather Research and Forecasting (WRF) model.

The OpenWRF products are generated using **EMS-WRF** with the **WRF-ARW (Advanced Research WRF)** non-hydrostatic dynamical core. The system is designed to provide detailed mesoscale and local-scale weather guidance, particularly in regions where global hydrostatic models are less effective at resolving local phenomena.

---

## Who runs it
- **Operator:** Independent individual operator (OpenSkiron.org)
- **Country / region:** Europe (multi-regional domains)

---

## What area it covers
- **Coverage:** Multiple regional domains across Europe and the Eastern Mediterranean
- **Example regions:**  
  Italy, France, Spain, Greece, Ionian Sea, Aegean Sea, Adriatic Sea, Cyprus, Israel, Turkey

---

## Model configuration
- **Model type:** Regional deterministic NWP
- **Model system:** WRF (Advanced Research WRF – ARW)
- **WRF framework:** EMS-WRF
- **Hydrostatic / non-hydrostatic:** Non-hydrostatic
- **Horizontal resolution:**  
  - Parent domains: ~12 km  
  - Nested domains: ~4 km
- **Initialization:**  
  Current atmospheric analysis data
- **Boundary conditions:**  
  GFS forecast data
- **Topography and land data:**  
  Pre-generated high-resolution static datasets tailored to each domain

---

## Update frequency and production
- **Nominal run times:**  
  Approximately twice daily (around 00 and 12 UTC), with domain-specific production schedules
- **Typical production time:**  
  ~50–80 minutes per run, plus ~15 minutes for post-processing and publication
- **Computing infrastructure:**  
  Parallel processing on multiple Linux servers and clusters, totaling ~124 physical CPU cores

Production times may occasionally vary depending on system load and upstream data availability.

---

## What it provides
OpenWRF GRIB products include the following fields:

- 10 m wind (speed and direction)
- Surface wind gusts
- Mean sea level pressure
- Accumulated precipitation
- Snow depth
- Total cloud cover
- Relative humidity
- Surface temperature
- Convective Available Potential Energy (CAPE)
- Simulated radar reflectivity
- Significant wave height
- Swell height, direction, and period
- Wind wave height, direction, and period
- Sea current speed and direction

---

## Marine and ocean components
- **Sea currents:**  
  Provided by the **Copernicus Marine Environment Monitoring Service**, generated using the **NEMO** ocean model
- **Wave data:**  
  Generated using the **WAM model (cycle 4.5.4)** driven by **ECMWF wind fields**, also sourced from Copernicus Marine services

These marine datasets are incorporated into the OpenWRF GRIB outputs alongside atmospheric fields.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official access point:**  
  https://www.openskiron.org/en/openwrf

---

## Notes
- OpenWRF is not an official national meteorological service model.
- The system emphasizes **mesoscale and local-scale realism**, complementing global models such as GFS that are optimized for synoptic-scale forecasting.
- Domain availability, resolution, and update timing may evolve over time as the system is maintained independently.

---

## Official information
- OpenWRF technical description and production notes (OpenSkiron.org)
