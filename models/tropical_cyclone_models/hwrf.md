# HWRF (Hurricane Weather Research and Forecasting Model)

## What this model is
The Hurricane Weather Research and Forecasting (HWRF) model is a specialized, atmosphere–ocean coupled tropical cyclone numerical weather prediction system developed by NOAA to forecast hurricane track, intensity, structure, and rainfall. It is a telescopic-nest, storm-following model built on the WRF-NMM dynamical core, designed to resolve the inner-core structure of tropical cyclones and their interaction with the ocean.

HWRF was NOAA's primary operational hurricane model from 2012 through the 2022 season. With the 2023 transition to the FV3-based [HAFS](hafs.md), HWRF moved to **legacy status** — it is frozen at its final pre-HAFS configuration and receives no further scientific upgrades — but it is **still run operationally for active tropical cyclones** as guidance alongside HAFS. As of mid-2026 no retirement date has been announced, and HWRF cycles are being produced in real time (live-verified July 2026).

---

## Who runs it
- **Organization:** NOAA / NWS / NCEP — Environmental Modeling Center (EMC, development) and NCEP Central Operations (NCO, operations)
- **Country / region:** United States

---

## When this model runs
HWRF runs **on demand for active tropical cyclones**, triggered by storm messages from the National Hurricane Center (NHC), Central Pacific Hurricane Center (CPHC), and/or Joint Typhoon Warning Center (JTWC). Each run is centered on a specific storm rather than a fixed geographic domain.

- **Cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Capacity:** up to **7 storms per cycle**

---

## What area it covers
- **Coverage:** Storm-centered, storm-following domains for tropical cyclones **worldwide** — all TCs that NHC and JTWC report to NCEP. Basins: North Atlantic (L), Eastern & Central Pacific (E/C), Western Pacific (W), North Indian Ocean (A/B), and Southern Hemisphere (S/P). *Live-verified July 2026: a single cycle simultaneously ran Eastern Pacific (`fausto06e`, `genevieve07e`) and Western Pacific (`noul11w`) storms.*
- **Storm selection:** Driven by official warning/invest messages from NHC / CPHC / JTWC. Invest areas use `9x`-series IDs (e.g. `invest98e`).

---

## Basic details
- **Model type:** Tropical cyclone NWP (deterministic), atmosphere–ocean coupled
- **Model core:** WRF-NMM (Non-hydrostatic Mesoscale Model; community NMM core, ~V4.0a)
- **Native model grid:** three telescopic, two-way interactive nests following the storm:
  - Parent domain (d01): ~13.5 km
  - Intermediate moving nest (d02): ~4.5 km
  - Inner moving nest (d03): ~1.5 km
- **Distributed output grids (all `regular_ll`; live-verified 2026-07-25):**

  | Output domain | Resolution | Grid (Ni × Nj) | Extent |
  |---|---|---|---|
  | `core` | 0.015° (~1.5 km) | 601 × 601 | ~9° × 9°, storm-centered |
  | `storm` | 0.015° (~1.5 km) | 1401 × 1401 | ~21° × 21°, storm-following |
  | `synoptic` | 0.125° (~13 km) | 961 × 721 | ~120° lon × 90° lat (70°N–20°S) |
  | `global` | 0.25° | 1440 × 721 | full globe |

- **Vertical levels:** distributed output on **45 pressure levels** (1000–2 hPa, verified). Native model: 75 vertical levels, model top ~10 hPa (documented; not independently verified here).
- **Forecast length:** 126 hours
- **Temporal output resolution:** pressure-level (`hwrfprs`) fields **3-hourly**; synthetic-satellite (`hwrfsat`) fields **6-hourly** (both f000–f126; live-verified)

---

## Initialization and data assimilation
HWRF employs a hurricane-specific initialization and data assimilation system:
- Advanced vortex initialization with vortex relocation and intensity/size/structure correction
- GSI-based hybrid ensemble–variational HWRF Data Assimilation System (HDAS) for the inner core
- Assimilation of conventional observations, satellite radiances, aircraft/reconnaissance data (HDOB, dropsondes) and Tail Doppler Radar (TDR) when available — HWRF was noted as the only operational model to use all reconnaissance data in the operational stream

Initial and boundary conditions come from the GFS/GDAS system, with 3-hourly GFS boundary conditions.

---

## Ocean / wave coupling
- **Ocean coupling (by basin):**
  - **MPIPOM-TC (Princeton Ocean Model)** for North Atlantic, Eastern Pacific, and Central Pacific storms
  - **HYCOM** for Western Pacific, North Indian Ocean, and Southern Hemisphere storms (JTWC basins)
  - Feature-based ocean initialization represents the loop current, warm/cold-core rings, and the storm-generated cold wake
- **Wave coupling:** one-way coupling to **WAVEWATCH III**, enabled for **North Atlantic, Eastern Pacific, and Central Pacific only**. Hurricane surface-wave products are generated and distributed for these three basins; the wave GRIB filter updates only when active storms exist in them. *Live-verified: `ww3.grb2` and wave bulletins/spectra are present for the Eastern Pacific storms but absent for the Western Pacific storm.*

---

## What it provides
Per storm, per cycle, the public NOMADS tree distributes (live-verified 2026-07-25). File naming: `{stormname}{NN}{basin}.{YYYYMMDDHH}.{product}` — e.g. `fausto06e.2026072512.hwrfprs.core.0p015.f000.grb2`. Tracker/stats products use the uppercase storm ID (e.g. `FAUSTO06E.…`).

**Gridded GRIB2 forecast fields** (each with a `.idx` sidecar):
- `hwrfprs.{core,storm,synoptic,global}.{res}.f###.grb2` — pressure-level and surface atmospheric fields (~748 messages, 45 pressure levels: winds, temperature, RH, geopotential, MSLP, CAPE/CIN, precipitation, PWAT, helicity, reflectivity, HWRF microphysics species, surface/flux fields), **3-hourly**
- `hwrfsat.{core,storm,synoptic,global}.{res}.f###.grb2` — synthetic satellite brightness temperature (~11 channels), **6-hourly**

**Storm-summary, track, and diagnostic products** (written at end of run):
- `swath.grb2` — storm-total swath summary
- `trak.hwrf.atcfunix` — **ATCF track and intensity guidance** (standard operational format)
- `trak.hwrf.3hourly`, `trak.hwrf.short6hr` — additional track files
- `hwrf_d03.htcf` — high-temporal-resolution (5-second) storm center/intensity tracker on the inner nest (d03)
- `stats.tpc` — TPC verification statistics
- `rainfall.ascii`, `wind10m.ascii`, `wind10hrly.ascii` — plain-text rainfall and 10 m wind summaries
- `wrfdiag_d01`, `wrfdiag_d02`, `wrfdiag_d03` — native WRF diagnostic files for the three nests

**Wave products (NATL / EPAC / CPAC only):**
- `ww3.grb2` (+ `.idx`) — WAVEWATCH III gridded wave output
- `ww3_bull.tar`, `ww3_cbull.tar`, `ww3_csbull.tar` — wave bulletins
- `ww3_spec.tar` — wave spectra

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work). Distributed via NOAA Open Data Dissemination (NODD): open to public use; NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (atmosphere, satellite, swath, waves; each with `.idx`), ATCF and other track text files (`trak.hwrf.*`), HTCF tracker, plain-text ASCII summaries, and TAR bundles (wave bulletins/spectra)
- **Official download location (NOMADS only — no AWS Open Data mirror):**
  - https://nomads.ncep.noaa.gov/pub/data/nccf/com/hwrf/prod/
  - FTP equivalent: ftp://ftp.ncep.noaa.gov/data/nccf/com/hwrf/prod/
  - Layout: `hwrf.YYYYMMDD/{00,06,12,18}/…`, with the storm ID as a filename prefix (no per-storm subdirectories). Real-time retention on NOMADS is short (roughly the two most recent days). The `inphwrf/` staging directory is present but currently empty.

---

## Notes
- **HWRF is legacy but still operational.** It was superseded as NOAA's primary hurricane system by [HAFS](hafs.md) in 2023 and is frozen at its final configuration, but it continues to run for active storms and remains a reference/consensus model. No retirement date has been announced as of mid-2026.
- HWRF uses storm-following, two-way interactive moving nests rather than a fixed domain, with tightly integrated atmosphere, ocean, data assimilation, and vortex-tracking components.
- Product availability depends on tropical cyclone activity — no active storms means no data. Wave products additionally require an active storm in a wave-coupled basin (NATL/EPAC/CPAC).
- The `hwrfsat` (6-hourly) fields are output at half the cadence of the `hwrfprs` (3-hourly) fields — plan retrieval accordingly.

---

## Relationship to other models
- **[HAFS](hafs.md):** Successor and current NOAA primary hurricane system (operational 2023). HWRF now runs as legacy guidance alongside it.
- **[HMON](hmon.md):** Ran operationally alongside HWRF as NOAA's dual-deterministic hurricane pair; also now legacy but still produced.
- **Predecessor lineage:** HWRF (operational 2007) succeeded the GFDL Hurricane Model; the modern successor chain is GFDL → HWRF/HMON → HAFS.
- An experimental Basin-scale HWRF (HWRF-B) has been run by AOML/HRD as a research alternative.

---

## Official documentation
- NCEP/EMC HWRF page: https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/hwrf.php
- EMC legacy HWRF implementation summary: https://emc.ncep.noaa.gov/gc_wmb/vxt/HWRF_legacy/about.php?branch=summary
- AOML HWRF page: https://www.aoml.noaa.gov/hurricane-weather-research-forecast-model/
- Biswas et al., *HWRF Scientific Documentation*
