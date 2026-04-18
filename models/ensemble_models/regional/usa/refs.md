# REFS (RRFS Ensemble Forecast System)

## What this model is
The RRFS Ensemble Forecast System (REFS) is the **ensemble component** of NOAA's next-generation Rapid Refresh Forecast System (RRFS).

REFS is a regional, convection-allowing ensemble designed to provide probabilistic short-range forecast guidance for high-impact weather across North America. It is built on the UFS framework and is intended to replace the legacy HREF and NARRE ensemble systems.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America
- **Domain details:**  
  REFS output is provided on the same subset grids as the deterministic RRFS:
  - CONUS (3 km)
  - Alaska (3 km)
  - Hawaii (2.5 km)
  - Puerto Rico (2.5 km)

---

## Basic details
- **Model type:** Regional ensemble NWP (convection-allowing)
- **Model system / core:** RRFS (UFS / FV3 in v1)
- **Horizontal resolution:**  
  - 3 km (CONUS, Alaska)  
  - 2.5 km (Hawaii, Puerto Rico)
- **Ensemble size:** Multiple members plus combined ensemble products (individual members distributed via the prototype AWS bucket; combined products distributed via NOMADS)
- **Forecast length:** Up to 60 hours
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**  
  - Hourly output to +60 h  
  - 3-hourly output beyond +60 h (where provided)

---

## Ensemble methodology
REFS uses a convection-allowing ensemble built on the UFS infrastructure and is designed to sample forecast uncertainty at storm-resolving scales. The ensemble includes time-lagged members and combined ensemble products used for probabilistic guidance.

The exact member composition and perturbation strategy may evolve as RRFS transitions from v1 to v2 (MPAS-based).

---

## What it provides
Probabilistic regional forecasts of:
- Precipitation amount, type, and thresholds
- Severe convection indicators
- Near-surface temperature, wind, and humidity
- Neighborhood and probability-based guidance for high-impact weather

REFS provides the primary short-range, convection-allowing probabilistic guidance across CONUS, Alaska, Hawaii, and Puerto Rico.

---

## Relationship to other models
REFS is intended to fully replace the following legacy NCEP ensemble systems:
- **HREF** (High-Resolution Ensemble Forecast)
- **NARRE** (North American Rapid Refresh Ensemble)

Compared to the legacy systems:
- REFS extends forecasts to **60 hours** (HREF ran to 48 hours)
- REFS provides **00, 06, 12, and 18 UTC** cycles for all regions, including non-CONUS domains (HREF only ran twice daily for AK, HI, and PR)
- NARRE's hourly 12-hour ensemble guidance is replaced by REFS's 60-hour forecasts updated every 6 hours

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  - https://nomads.ncep.noaa.gov/ (once fully operational)  
  - https://registry.opendata.aws/noaa-rrfs/ (prototype data, including individual member output and combined ensemble products)

---

## Status
- Proposed retirement of HREF and NARRE was announced in NWS Public Information Statement 25-41 (June 26, 2025).
- Targeted for operational implementation alongside the deterministic RRFS, originally "early 2026."
- As of April 2026, REFS is in late-stage pre-operational status; prototype and retrospective data are available via the NOAA Open Data Dissemination (NODD) program on AWS.

---

## Notes
- In the prototype AWS distribution, individual ensemble members are stored under directories such as `rrfs_a/rrfsens.<date>/<cycle>/m###`, while combined ensemble products are stored under `rrfs_public/refs.<date>/<cycle>/ensprod/` directories reflecting the planned operational NOMADS structure.
- As with all ensemble systems, REFS output should be interpreted probabilistically rather than as a single deterministic forecast.
- The member composition and ensemble design are expected to evolve as RRFS transitions to v2 with the MPAS dynamical core.

---

## Official documentation
- NWS Public Information Statement 25-41 (PDF):  
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- HREF-to-REFS product changes:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/href_product_changes.txt
- NARRE-to-REFS product changes:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/narre_replacement.txt
- NOAA RRFS/REFS prototype on AWS:  
  https://registry.opendata.aws/noaa-rrfs/
