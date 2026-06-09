# HiresW (High-Resolution Window Forecast System)

## What this model is
HiresW (the High-Resolution Window Forecast System, sometimes written HiResW or HRW) is NOAA/NCEP's dual-core, convection-allowing regional NWP system. It runs two dynamical cores — WRF-ARW and FV3 — over five fixed regional domains, providing short-range high-resolution guidance for the U.S. and its offshore/territorial regions.

> **Status note (read first):** Under NWS Service Change Notice 26-48 (May 12, 2026), the **CONUS, Alaska, Hawaii, and Puerto Rico** HiresW domains are scheduled to retire on **August 31, 2026 at 12 UTC**, replaced by [RRFS](./rrfs.md) (deterministic) and [REFS](../../../ensemble_models/regional/usa/refs.md) (ensemble). The **Guam** domain is explicitly preserved because RRFS does not extend to Guam. This entry documents the full system as operated up to that transition; see the [Status and retirement](#status-and-retirement) section below.

---

## Who runs it
- **Organization:** NOAA / National Weather Service — NCEP (Environmental Modeling Center develops; NCEP Central Operations runs it in the production suite)
- **Country / region:** United States (CONUS and offshore/territorial domains)

---

## What area it covers
- **Coverage:** Five fixed regional domains:
  - **CONUS** — contiguous United States
  - **Alaska** (`ak`)
  - **Hawaii** (`hi`)
  - **Guam** (`guam`) — western Pacific
  - **Puerto Rico** (`pr`) — Caribbean
- **Domain details:** Output grids are on a Lambert Conformal projection for CONUS; domains are run on a staggered cycle schedule (see below), not all simultaneously.

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing, dual-core, multi-member)
- **Model system / core:** Two independent dynamical cores:
  - **WRF-ARW** (Advanced Research WRF; developed and maintained by NCAR)
  - **FV3** (Finite-Volume Cubed-Sphere, limited-area configuration; UFS lineage). This core replaced the earlier NMMB core in the HiresW system.
- **Dynamical formulation:** Non-hydrostatic (both cores)
- **Convection-allowing:** Yes — deep convection is explicitly resolved; no cumulus parameterization
- **Horizontal resolution:**
  - Native computational grid ~3–4 km (varies by core and domain)
  - Output is distributed remapped onto **two grids per run: a ~5 km grid and a ~2.5 km grid**
- **Vertical levels:** WRF-ARW ~50 levels (raised from 40 in May 2015); FV3 not clearly documented in public NCEP materials
- **Forecast length:**
  - WRF-ARW: **48 hours**
  - FV3: **60 hours**
- **Update frequency / cycles:** 2× daily per domain, on a staggered schedule:
  - **00 UTC and 12 UTC:** CONUS, Hawaii, Guam
  - **06 UTC and 18 UTC:** Alaska, Puerto Rico
- **Temporal output resolution:** Hourly

---

## Members and cores
HiresW produces several quasi-independent runs per domain, which is why it is often referred to as a small multi-model collection rather than a single model:

- **ARW member 1** — the primary WRF-ARW run (all five domains)
- **ARW member 2** (`mem2`) — a second WRF-ARW run with different initial/boundary sourcing. **Not run for Guam** (no NAM coverage for that domain)
- **FV3** — a single FV3 run (all five domains; no second member)

These runs feed directly into the [HREF](../../../ensemble_models/regional/usa/href.md) regional ensemble as members.

---

## Initial and boundary conditions
- **WRF-ARW member 1:**
  - CONUS and Puerto Rico: initial conditions from [RAP](./rap.md); lateral boundary conditions from [GFS](../../global/usa/gfs.md)
  - Hawaii, Guam, Alaska: initial and lateral boundary conditions both from GFS
- **WRF-ARW member 2:** initial and lateral boundary conditions from [NAM](./nam.md) (only where NAM coverage exists; not Guam)
- **FV3 (all domains):** initial and lateral boundary conditions interpolated from GFS

---

## What it provides
Deterministic convection-allowing forecasts of:
- Near-surface temperature, humidity, wind, and pressure
- Precipitation and precipitation type (explicitly resolved convection)
- Simulated/composite radar reflectivity
- Convective diagnostics (CAPE, CIN, helicity, updraft helicity)
- Cloud and hydrometeor fields
- Aviation-relevant fields (ceiling, visibility, wind gusts)

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work; CC0-equivalent)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (each file accompanied by a `.idx` index; BUFR soundings also produced)
- **Official download location:**
  - NOMADS (real-time, rolling retention of the most recent several days):
    https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/
  - Product information page:
    https://www.nco.ncep.noaa.gov/pmb/products/hiresw/

### File naming convention
Files live under a per-day directory `hiresw.YYYYMMDD/` and follow:

```
hiresw.t{CC}z.{core}_{grid}.f{FFF}.{domain}.grib2
```

- `{CC}` — cycle: `00`, `06`, `12`, `18`
- `{core}` — `arw` or `fv3`
- `{grid}` — `5km` or `2p5km`
- `{FFF}` — forecast hour (`00`–`48` for ARW, `00`–`60` for FV3)
- `{domain}` — `conus`, `ak`, `hi`, `guam`, `pr`
- ARW member 2 appends `mem2` to the domain token: `conusmem2`, `himem2`, `akmem2`, `prmem2` (no `guammem2`)

Examples:
```
hiresw.t00z.arw_5km.f12.conus.grib2
hiresw.t00z.arw_5km.f12.conusmem2.grib2     # ARW member 2
hiresw.t00z.fv3_2p5km.f48.hi.grib2
hiresw.t06z.arw_5km.f24.ak.grib2
```

Download examples:
```bash
# List one cycle's files for a given day
curl -s https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/hiresw.20260608/ | grep 't00z.fv3_5km.*conus'

# Fetch a single forecast hour (FV3, 5 km CONUS, F24, 00Z run)
curl -O https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/hiresw.20260608/hiresw.t00z.fv3_5km.f24.conus.grib2
```

---

## Relationship to other models
- **[HREF](../../../ensemble_models/regional/usa/href.md):** HiresW supplies the foundational members of HREF (ARW member 1, ARW member 2, and FV3, current and time-lagged) across all HREF domains.
- **[NAM](./nam.md) / [NAM Nest](./nam-nest.md):** NAM provides the initial and boundary conditions for HiresW ARW member 2, and the NAM 3 km CONUS nest is a fellow HREF member.
- **[RAP](./rap.md):** Supplies initial conditions for ARW member 1 over CONUS and Puerto Rico.
- **[GFS](../../global/usa/gfs.md):** Supplies boundary conditions for ARW and both initial/boundary conditions for FV3.
- **[RRFS](./rrfs.md) / [REFS](../../../ensemble_models/regional/usa/refs.md):** Replacements for the retiring CONUS/AK/HI/PR domains (see below).

---

## Status and retirement
- **CONUS, Alaska, Hawaii, Puerto Rico:** scheduled for retirement **August 31, 2026 at 12 UTC** under NWS SCN 26-48 (companion product-retirement list in SCN 26-47), originally proposed in NWS PNS 25-41 (June 26, 2025). Replaced by [RRFS](./rrfs.md) (CONUS/AK at 3 km; HI/PR at 2.5 km) and the [REFS](../../../ensemble_models/regional/usa/refs.md) ensemble. Subject to the standard CWD/ECE contingency.
- **Guam:** explicitly **preserved** — RRFS does not extend to the Guam domain, leaving HiresW as the operational convection-allowing guidance source for the western Pacific after the other domains retire.

---

## Notes
- HiresW is best understood as a small collection of convection-allowing runs (two ARW members + one FV3) rather than a single model; its main downstream role is as the backbone membership of [HREF](../../../ensemble_models/regional/usa/href.md).
- Both output grids (5 km and 2.5 km) are remapped products of the same underlying forecast; the 2.5 km files have a documented quirk of bundling multiple lead-time steps per forecast hour in some fields — verify the `valid_time`/`forecast_time` when subsetting.
- The FV3 core in HiresW replaced an earlier NMMB core; older NCEP documentation occasionally labels the second core with NMMB-era terminology.

---

## Official documentation
- NCEP product page: https://www.nco.ncep.noaa.gov/pmb/products/hiresw/
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/
- NWS SCN 26-48 (RRFS/REFS implementation; retirement of CONUS/AK/HI/PR HiresW domains, Guam preserved): https://www.weather.gov/media/notification/pdf_2026/scn26-48_RRFS_and_REFS_Implementation.pdf
- NWS PNS 25-41 (legacy model cessation; Guam exempted): https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- WRF-ARW (NCAR): https://www.mmm.ucar.edu/models/wrf
- FV3 (NOAA/GFDL): https://www.gfdl.noaa.gov/fv3/
