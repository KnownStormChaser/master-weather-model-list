# NSSL MPAS (Experimental)

## What this model is
The NSSL MPAS runs are a set of experimental, real-time, convection-allowing forecasts run by NOAA's National Severe Storms Laboratory using the Model for Prediction Across Scales (MPAS) dynamical core. They are part of a collaborative effort between NSSL, NOAA's Global Systems Laboratory (GSL), and NCAR to develop the MPAS-based configuration intended for the second version of the [Rapid Refresh Forecast System (RRFS)](./rrfs.md). The runs are distributed as three configurations that share a common codebase and 3 km CONUS domain but differ in initialization source and microphysics, allowing direct comparison of physics choices and of MPAS against the current operational convection-allowing suite.

These are experimental, pre-operational research runs — not an operational NWP product — but they are run continuously on a fixed schedule rather than as one-off case studies, and they represent the development line that is expected to become RRFSv2.

---

## Who runs it
- **Organization:** NOAA / National Severe Storms Laboratory (NSSL), Forecast Research and Development Division (FRDD); developed collaboratively with NOAA/GSL and NCAR
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Contiguous United States (CONUS)
- **Domain details:** 3 km regional MPAS mesh covering CONUS. MPAS uses an unstructured centroidal Voronoi mesh with C-grid staggering; the NSSL runs use GSL's MPAS codebase.

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing), experimental
- **Model system / core:** MPAS (Model for Prediction Across Scales), atmospheric component, with a subset of Advanced Research WRF (ARW) physics
- **Dynamical formulation:** Non-hydrostatic, finite-volume on an unstructured Voronoi mesh
- **Convection-allowing:** Yes — 3 km cell spacing; no cumulus parameterization
- **Horizontal resolution:** ~3 km cell spacing (CONUS mesh)
- **Vertical levels:** TBD
- **Forecast length:** ~48 h
- **Update frequency / cycles:** Varies by configuration (see table below)
- **Temporal output resolution:** Hourly GRIB2 output (TBD if sub-hourly fields are included)

### Configurations

| Configuration | File tag | Initialized from | Microphysics | Cycles | Status |
|---|---|---|---|---|---|
| MPAS-HTPO-NSSL | `mpasht2` | Operational [HRRR](./hrrr.md) | TEMPO (Thompson-Eidhammer for Operations) | 4× daily (00/06/12/18 UTC) | Active |
| MPAS-RN-NSSL | `mpasrn` | Experimental RRFS (EMC) | NSSL 2-moment | 2× daily (00/12 UTC) | Discontinued (last cycle 2026-06-16) |
| MPAS-RN3-NSSL | `mpasrn3` | Experimental RRFS (EMC) | NSSL 3-moment | 2× daily (00/12 UTC) | Discontinued (last cycle 2026-03-16) |

As of mid-July 2026, only MPAS-HTPO-NSSL (`mpasht2`) is still being published. The two RRFS-initialized configurations have been retired — MPAS-RN3-NSSL ended 2026-03-16 and MPAS-RN-NSSL ended 2026-06-16 — leaving the HRRR-initialized run as the sole active NSSL MPAS configuration.

---

## Data assimilation
- **Data assimilation:** No — the NSSL MPAS runs are cold-start. They do not perform their own analysis.

---

## Initial and boundary conditions
- **Initial conditions:** Parent-model fields — operational HRRR (MPAS-HTPO) or the experimental deterministic RRFS provided by EMC (MPAS-RN / MPAS-RN3)
- **Boundary conditions:** TBD (inherited from the respective parent system)

---

## What it provides
Deterministic convection-allowing forecasts of standard atmospheric fields (temperature, wind, precipitation, pressure, humidity, reflectivity/simulated radar, severe-weather diagnostics such as updraft helicity), distributed as GRIB2.

---

## Data availability
- **Is the data free?** Yes
- **License:** "Freely available" — the rights statement carried in the dataset's own THREDDS/ISO 19115 metadata (publisher: DOC/NOAA/OAR/NSSL). As a U.S. Government work it is effectively public domain (CC0-equivalent). Note: the CC BY 4.0 banner in the THREDDS server's page header is generic boilerplate describing the NSSL ATD radar archive and does **not** apply at the MPAS dataset level.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (gzipped per-forecast-hour files bundled in per-cycle `.tar` archives)
- **Official download location:**
  - Browse (THREDDS catalog): https://data.nssl.noaa.gov/thredds/catalog/FRDD/NSSL-MPAS/catalog.html
  - Direct download (HTTPServer): `https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/<YYYY>/<YYMMDDHH>_<tag>.tar`
    - e.g., `.../2026/26060718_mpasht2.tar`
  - Each `.tar` contains files named `<tag>_nssl_YYYYMMDDHHfFF.grib2.gz`
- **Metadata:** ISO 19115 record available per dataset via the THREDDS `iso/` service

---

## Notes
- **Related GSL run (not separately cataloged):** GSL runs a companion MPAS configuration, **MPAS-RRFSA-GSL** (initialized from RRFS-A, TEMPO microphysics), plus reduced/higher-resolution variants (3.5 km MPASLR-RRFSA-GSL, 1.25 km). These are displayed on the NSSL CAMs viewer and Pivotal Weather but are **not** published to PARR and have no open gridded feed found, so they are excluded under the repository's downloadable-data requirement.
- **Display/viewer:** Rendered imagery for all of these runs is viewable at the NSSL CAMs site (https://cams.nssl.noaa.gov/) and via Pivotal Weather; those are visualization front-ends, not the data feed.
- **Relationship to RRFS:** This is the MPAS development line for [RRFSv2](./rrfs.md). The operational [RRFSv1](./rrfs.md) and its ensemble [REFS](../../../ensemble_models/regional/usa/refs.md) use the FV3-LAM core; RRFSv2 is planned to transition to MPAS.
- **MPAS run history:** MPAS runs began at NSSL in early January 2023 and at GSL in fall 2024. The NSSL runs now use GSL's MPAS codebase.

---

## Status
- Experimental / pre-operational. GSL states these systems are not for operational use and the supporting websites are not maintained 24/7.
- Only MPAS-HTPO-NSSL (`mpasht2`) remains active as of mid-July 2026. MPAS-RN3-NSSL was discontinued after 2026-03-16 (it did not resume following NSSL's Jet→Ursa HPC transition) and MPAS-RN-NSSL was discontinued after 2026-06-16.
- NSSL's legacy 4 km WRF-NSSL run was scheduled to cease on or near 31 March 2026 (Jet decommissioning) as resources shifted toward MPAS development.
- Expected to feed the eventual operational RRFSv2; once RRFSv2 is implemented and lands on NOMADS/AWS, a separate operational entry will be warranted.

---

## Official documentation
- PARR (NSSL Data Repository) THREDDS root: https://data.nssl.noaa.gov/thredds/catalog/catalog.html
- NSSL CAMs (viewer + run descriptions): https://cams.nssl.noaa.gov/
- GSL Predictions / RRFSv2 development: https://gsl.noaa.gov/research/predictions/
- MPAS model home (NCAR): https://mpas-dev.github.io/
- RRFSv2 dynamical-core background — Carley, J., et al. (2023), NOAA/EMC Office Note 516: https://doi.org/10.25923/ccgj-7140
- NSSL Forecast webpage: https://www.nssl.noaa.gov/tools/forecast/
- Data contact / publisher: DOC/NOAA/OAR/NSSL — nssl.support@noaa.gov
