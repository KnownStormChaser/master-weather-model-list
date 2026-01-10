# ICON-2I (Italy – High Resolution)

## What this model is
ICON-2I is a **high-resolution, convection-permitting regional numerical weather prediction (NWP) system** based on the ICON modeling framework and operated in Italy.

It represents the Italian implementation of ICON, designed to resolve small-scale phenomena such as deep convection, intense precipitation, and local wind systems over complex terrain.

---

## Who runs it
- **Organization:** Agenzia ItaliaMeteo  
- **Country / region:** Italy  
- **Model core developed by:** Deutscher Wetterdienst (DWD)

---

## What area it covers
- **Coverage:** Entire Italian territory and surrounding areas  
- **Horizontal resolution:** **2.2 km**  
- **Domain:** Single national domain covering all of Italy

---

## Basic details
- **Model type:** Regional deterministic NWP (non-hydrostatic, convection-permitting)
- **Model system:** ICON
- **Horizontal resolution:** 2.2 km
- **Forecast length:** Up to **72 hours**
- **Primary update times:** 00 and 12 UTC

Additional configurations include:
- **ICON-2I-RUC:** rapid-update deterministic runs (+24 h)
- **ICON-2I-EPS:** ensemble system (20 members, +51 h)

---

## Data assimilation
Initial conditions are generated using the **KENDA-LETKF** ensemble data assimilation system with **hourly assimilation cycles**.

Assimilated observations include:
- surface observations (SYNOP)
- radiosoundings (TEMP)
- aircraft observations (AMDAR, AIREP, ACARS)
- national radar network data:
  - reflectivity volumes
  - radial wind
- radar-derived precipitation fields via **Latent Heat Nudging (LHN)**

Assimilation is performed up to approximately **200 hPa**.

---

## Boundary and initial conditions
- **Lateral boundary conditions:** ECMWF IFS-HRES
- **Initial background:** ICON-based ensemble provided through KENDA

---

## Data availability
- **Is the data free?** Yes  
- **Is the data downloadable?** Yes  
- **Data formats:** GRIB2  
- **Official download location:**  
  https://dati.agenziaitaliameteo.it/dataset/previsioni-meteorologiche-modello-icon-2i

---

## Notes
ICON-2I became **fully operational in June 2024** and represents Italy’s primary national convection-permitting NWP system, comparable in role to ICON-D2 (Germany) or AROME (France).

Publicly available datasets provide a curated subset of operational forecast fields.

---

## Official documentation
- https://dati.agenziaitaliameteo.it  
