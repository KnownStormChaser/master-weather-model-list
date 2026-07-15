# HiresW (High-Resolution Window Forecast System)

## What this model is
HiresW (the High-Resolution Window Forecast System, sometimes written HiResW or HRW) is NOAA/NCEP's dual-core, convection-allowing regional NWP system. It runs two dynamical cores — WRF-ARW and FV3 — over five fixed regional domains, providing short-range high-resolution guidance for the U.S. and its offshore/territorial regions.

> **Status note (read first):** Under NWS Service Change Notice 26-47 (termination notice; updated July 6, 2026), the **CONUS, Alaska, Hawaii, and Puerto Rico** HiresW domains are scheduled to retire on **October 6, 2026 at 12 UTC**, replaced by [RRFS](./rrfs.md) (deterministic) and [REFS](../../../ensemble_models/regional/usa/refs.md) (ensemble) under companion SCN 26-48. The **Guam** domain is explicitly preserved because RRFS does not extend to Guam. This entry documents the full system as operated up to that transition; see the [Status and retirement](#status-and-retirement) section below.

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
- **Domain details:** Output projection varies by domain (verified from live GRIB2 headers, 2026-07-15): CONUS on Lambert Conformal, Alaska on polar stereographic, and Guam/Hawaii/Puerto Rico on Mercator (finer grids) or regular lat-lon (5 km grids). Domains are run on a staggered cycle schedule (see below), not all simultaneously.

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
  - Output is distributed on **two grids per run: a ~5 km grid and a finer grid**. The finer grid is **2.5 km for CONUS, Hawaii, Guam, and Puerto Rico**, but **3 km for Alaska** (distributed under the `3km` grid token). Verified grid geometry from live GRIB2 headers (2026-07-15):
    - CONUS — 5 km: Lambert 1473×1025 @ 5.079 km; 2.5 km: Lambert 2145×1377 @ 2.540 km
    - Alaska — 5 km: polar stereographic 825×603 @ 5.000 km; 3 km: polar stereographic 1649×1105 @ 2.976 km
    - Guam — 2.5 km: Mercator 193×193 @ 2.500 km (5 km: regular lat-lon 223×170)
    - Hawaii — 2.5 km: Mercator 321×225 @ 2.500 km (5 km: regular lat-lon 223×170)
    - Puerto Rico — 2.5 km: Mercator 177×129 @ 2.500 km (5 km: regular lat-lon 340×208)
- **Vertical levels:** WRF-ARW ~50 levels (raised from 40 in May 2015). FV3 native level count is not documented in public NCEP materials and **cannot be determined from NOMADS output** (output is on pressure levels, not native model levels). GRIB2 output carries **27 isobaric levels, 1000–200 mb** (25 mb spacing below 500 mb, 50 mb above) for both cores — verified 2026-07-15.
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
- **Data formats:** GRIB2 (each file accompanied by a `.idx` index). **BUFR soundings** are also produced per domain/core/cycle, distributed both as `bufr_{domain}{core}.t{CC}z/` station directories and as bundled `hiresw.t{CC}z.{domain}{core}.bufrsnd.tar.gz` tarballs (verified in the NOMADS directory, 2026-07-15).
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
- `{grid}` — `5km` (all domains), `2p5km` (CONUS/Hawaii/Guam/Puerto Rico), or `3km` (Alaska only)
- `{FFF}` — forecast hour (`00`–`48` for ARW, `00`–`60` for FV3)
- `{domain}` — `conus`, `ak`, `hi`, `guam`, `pr`
- ARW member 2 appends `mem2` to the domain token: `conusmem2`, `himem2`, `akmem2`, `prmem2` (no `guammem2`). **Member 2 is distributed only on the 5 km grid** — the finer 2.5 km / 3 km grids carry member 1 (ARW) and FV3 only (verified 2026-07-15).

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
- **CONUS, Alaska, Hawaii, Puerto Rico:** scheduled for retirement **October 6, 2026 at 12 UTC** under NWS SCN 26-47 (termination notice; RRFS/REFS implementation under companion SCN 26-48), originally proposed in NWS PNS 25-41 (June 26, 2025). The July 6, 2026 SCN update moved the date from August 31, 2026 to October 6, 2026. Replaced by [RRFS](./rrfs.md) (CONUS/AK at 3 km; HI/PR at 2.5 km) and the [REFS](../../../ensemble_models/regional/usa/refs.md) ensemble. Subject to the standard CWD/ECE contingency.
- **Guam:** explicitly **preserved** — RRFS does not extend to the Guam domain, leaving HiresW as the operational convection-allowing guidance source for the western Pacific after the other domains retire.

---

## Notes
- HiresW is best understood as a small collection of convection-allowing runs (two ARW members + one FV3) rather than a single model; its main downstream role is as the backbone membership of [HREF](../../../ensemble_models/regional/usa/href.md).
- The 5 km and finer (2.5 km / 3 km) grids are remapped products of the same underlying forecast. The **finer files come in 3-hour blocks**: at forecast hours divisible by 3 (f03, f06, … f60) the file bundles the valid hour plus the two preceding hourly steps for the instantaneous fields, while off-block hours (f01, f02, f04 …) contain only the single valid hour. The 5 km files always contain just the single valid hour. Verify `forecast_time`/`valid_time` when subsetting the finer grids (confirmed 2026-07-15 on CONUS/Guam/Hawaii 2.5 km and Alaska 3 km: the f24 file carries hours 22–24; f12 carries 10–12).
- The FV3 core in HiresW replaced an earlier NMMB core; older NCEP documentation occasionally labels the second core with NMMB-era terminology.

---

## Official documentation
- NCEP product page: https://www.nco.ncep.noaa.gov/pmb/products/hiresw/
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/
- NWS SCN 26-47 (termination of CONUS/AK/HI/PR HiresW domains, Guam preserved; effective October 6, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-47_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS.aaa.pdf
- NWS SCN 26-48 (RRFS/REFS implementation; effective October 6, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-048_RRFS_and_REFS_Implementation.aab.pdf
- NWS PNS 25-41 (legacy model cessation; Guam exempted): https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- WRF-ARW (NCAR): https://www.mmm.ucar.edu/models/wrf
- FV3 (NOAA/GFDL): https://www.gfdl.noaa.gov/fv3/
