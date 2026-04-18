# HiresW Guam (High Resolution Window — Guam Domain)

## What this model is
HiresW Guam is a high-resolution convection-allowing numerical weather prediction system covering Guam and the surrounding western Pacific.

It is the **only remaining HiresW domain** following the retirement of the CONUS, Alaska, Hawaii, and Puerto Rico HiresW domains as part of the RRFSv1 transition (NWS Public Information Statement 25-41). HiresW Guam was explicitly exempted from that retirement because RRFS does not extend to the Guam domain, leaving HiresW as the operational source of convection-allowing guidance for the region.

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States (Guam and surrounding western Pacific)

---

## What area it covers
- **Coverage:** Guam and surrounding western Pacific waters
- **Domain role:** The only HiresW domain continuing in operations after the NAM/HiresW/HREF retirements

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing, dual-core)
- **Horizontal resolution:** ~3 km
- **Convection handling:** Explicitly resolved (no cumulus parameterization)
- **Dynamical cores:** Dual-core system
  - WRF-ARW (Advanced Research WRF)
  - FV3 (Finite-Volume Cubed-Sphere, limited-area configuration)
- **Forecast length:**
  - WRF-ARW: 48 hours
  - FV3: 60 hours
- **Update frequency:** 2× daily (00 and 12 UTC)

---

## Initial and boundary conditions
Unlike the retired HiresW CONUS and Puerto Rico domains, the Guam domain does not run a second WRF-ARW member initialized from NAM, because NAM does not cover Guam.

- **Primary WRF-ARW run:**
  - Initial conditions: GFS
  - Lateral boundary conditions: GFS
- **FV3 run:**
  - Initial and lateral boundary conditions: interpolated from GFS

---

## What it provides
Deterministic high-resolution forecasts of:
- Near-surface temperature, humidity, wind, and pressure
- Precipitation (explicitly resolved convective systems)
- Cloud and hydrometeor fields
- Tropical convection and rainfall guidance specific to the western Pacific

HiresW Guam output is the primary convection-allowing guidance for NWS forecast operations in the region, supporting severe weather, aviation, and tropical forecasting.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/

---

## Relationship to other models
HiresW Guam is operationally isolated in an unusual way. With RRFS not covering Guam, and the other HiresW domains being retired, it is the only U.S. operational CAM guidance source for its region.

For the retired HiresW domains (CONUS, Alaska, Hawaii, Puerto Rico), convection-allowing guidance is now provided by **RRFS** and probabilistic guidance by **REFS**.

The dual-core design (WRF-ARW and FV3) reflects HiresW's historical role as a test bed for different dynamical cores and provides a rough ensemble-like comparison within a single operational system, though it is not formally an ensemble.

---

## Notes
- The WRF-ARW is developed and supported by the National Center for Atmospheric Research (NCAR). More information: https://www.mmm.ucar.edu/weather-research-and-forecasting-model
- The FV3 dynamical core originated at NOAA/GFDL and is now part of the broader Unified Forecast System (UFS). More information: https://www.gfdl.noaa.gov/fv3/
- HiresW Guam's long-term future is not publicly documented. As NOAA's UFS-based regional modeling strategy evolves (for example, toward an eventual RRFSv2 on the MPAS dynamical core), it is plausible that the Guam domain could eventually be absorbed into a Pacific RRFS extension or equivalent. No formal notification has been issued as of April 2026.

---

## Official documentation
- NOMADS HiresW product description: https://nomads.ncep.noaa.gov/
- NWS PNS 25-41 (retirement of other HiresW domains, with Guam explicitly exempted): https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
