# HAFS (Hurricane Analysis and Forecast System)

## What this model is
The Hurricane Analysis and Forecast System (HAFS) is a **specialized, coupled tropical cyclone numerical weather prediction system** developed within NOAA’s **Unified Forecast System (UFS)** framework.

HAFS is designed specifically to forecast **tropical cyclones and their associated hazards**, using storm-centered, moving high-resolution nests to resolve the inner-core structure, intensity change, and air–sea interactions.

---

## Who runs it
- **Organization:** National Centers for Environmental Prediction (NCEP)  
- **Country / region:** United States  
- **Developed by:** NOAA EMC and the UFS Hurricane Application Team

---

## When this model runs
HAFS **runs only when an active tropical cyclone exists**.

Each forecast is triggered by official storm messages and is tied to a specific storm and forecast cycle rather than a fixed geographic domain.

---

## What area it covers
- **Coverage:** Storm-centered moving domains following tropical cyclones  
- **Basins supported:**  
  - North Atlantic (NATL)  
  - Eastern & Central Pacific (EPAC, CPAC)  
  - Western Pacific (WPAC)  
  - North Indian Ocean (NIO)  
  - Southern Hemisphere (SH)

---

## Basic details
- **Model type:** Tropical cyclone–specific NWP system
- **Framework:** Unified Forecast System (UFS)
- **Atmospheric core:** FV3
- **Coupling:** Atmosphere–ocean–wave (CMEPS)
- **Horizontal resolution (typical):**
  - Parent domain: ~13 km
  - Moving nest(s): ~1.8–5.4 km
- **Forecast length:** **5.25 days (126 hours)**
- **Update frequency:** Every 6 hours (00, 06, 12, 18 UTC per active storm)

---

## Data assimilation and initialization
HAFS employs **advanced tropical cyclone–specific data assimilation**, including:
- vortex relocation and initialization
- inner-core data assimilation
- aircraft reconnaissance (TDR, HDOB, dropsondes)
- satellite-derived winds (AMVs)
- GPS radio occultation
- storm-following incremental analysis updates (IAU)

Assimilation is explicitly designed to improve **intensity change, rapid intensification, and storm structure** forecasts.

---

## Earth-system components
Depending on version and configuration, HAFS includes:
- **Atmosphere:** FV3
- **Ocean:** MOM6 and/or HYCOM
- **Waves:** WAVEWATCH III
- **Sea ice:** CICE (optional)
- **Land:** Noah-MP

---

## What it predicts
- tropical cyclone track
- maximum winds and minimum pressure
- storm structure and size
- precipitation and rainfall totals
- surface winds and hazards
- storm-generated waves and air–sea interaction effects

Primary outputs include **ATCF track and intensity guidance**, along with gridded forecast fields.

---

## Relationship to other models
HAFS is NOAA’s **operational hurricane forecast system**, replacing:
- **HWRF** and **HMON** (in 2023)
- earlier experimental FV3-based hurricane systems

It is tightly integrated with NOAA’s global modeling and data assimilation infrastructure.

---

## Data availability
- **Is the data free?** Yes  
- **Is the data downloadable?** Yes  
- **Data formats:** GRIB2, ATCF  
- **Official download locations:**  
    https://nomads.ncep.noaa.gov/pub/data/nccf/com/hafs/prod/

---

## Notes
HAFS output availability depends on tropical cyclone activity and operational scheduling. The system is **not intended for general weather forecasting outside active storms**.

---

## Official documentation
- https://wpo.noaa.gov/the-hurricane-analysis-and-forecast-system-hafs/
- https://github.com/hafs-community/HAFS
