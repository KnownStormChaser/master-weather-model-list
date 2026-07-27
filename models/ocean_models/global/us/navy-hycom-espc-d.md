# Navy Global HYCOM (ESPC-D)

## What this model is
This entry covers the **U.S. Navy's global operational ocean/sea-ice forecast** as distributed publicly through NOAA's NOMADS server under `navo/prod/hycom`. The product is **ESPC-D V02** — the deterministic configuration of the Navy's **Earth System Prediction Capability**, a two-way coupled **NAVGEM (atmosphere) – HYCOM (ocean) – CICE (sea ice)** system. The ocean core is **HYCOM 2.2.99**, the ice core is **CICE 5.1.2**, run as experiment `03.1` (verified from the NetCDF `generating_model` attribute: `ESPC-D V02: HYCOM 2.2.99, CICE 5.1.2, expt_03.1`).

ESPC-D is the operational successor to, and is built on the ocean/ice components of, **GOFS 3.1** (Global Ocean Forecast System v3.1) — the 1/12° HYCOM+CICE system declared operational on 7 November 2018. ESPC-D V02 adds two-way atmosphere–ocean–ice coupling and **astronomical tidal forcing in HYCOM**, which generates internal tides visible in the sea surface height / steric SSH fields.

The same HYCOM ocean core is run independently by NOAA — with GFS rather than NAVGEM forcing — as [Global RTOFS](./rtofs-global.md). This Navy system also provides lateral boundary conditions to the Navy's regional nests, including [regional NCOM](../../regional/us/ncom.md) (AMSEAS, USEAST, and others).

> **Naming / scope note:** "HYCOM" is a model *core* used by several systems (Navy global, NOAA RTOFS, HYCOM Consortium reanalyses). This entry is specifically the **Navy global operational HYCOM as ESPC-D V02, as published on NOMADS `navo/prod`**. It is not the HYCOM Consortium's research/reanalysis data server products, nor GOFS 3.1 as a standalone dataset (both distributed elsewhere).

---

## Who runs it
- **Production Unit:** **Fleet Numerical Meteorology and Oceanography Center (FNMOC)** (NetCDF `institution` attribute); run at the Navy DoD Supercomputing Resource Center under FNMOC control
- **Country:** United States
- **Developed by:** U.S. Naval Research Laboratory (HYCOM, NAVGEM); CICE from Los Alamos National Laboratory; coupled via ESMF/NUOPC
- **Public redistribution:** NOAA / NCEP via NOMADS (`navo/prod`); NOAA NCEI catalogs it as "FNMOC Navy Global Hybrid Coordinate Ocean Model"
- **Role in any larger system:** The Navy's global "first look" ocean/ice forecast; provides boundary conditions to Navy regional models (regional NCOM / AMSEAS / USEAST); one-way coupled to WaveWatch III

---

## What area it covers
The public NOMADS distribution is a **subset** of the full global ESPC-D output — a global surface field plus four Northern-Hemisphere full-depth patches. All are delivered on the `glby0.08` (1/12°) product grid: a regular lat-lon grid at **0.08° longitude × 0.04° latitude** (verified). Longitudes below converted from the files' 0–360°E convention.

| Area file | Content | Grid (lon × lat × depth) | Longitude | Latitude |
|-----------|---------|--------------------------|-----------|----------|
| `sfc_u` | **Global surface** (top layer only) | 4500 × 4001 × 1 | full 0–360°E | 80°S – 80°N |
| `regp01` | 3-D, NW Atlantic / U.S. East / Gulf / Caribbean | 626 × 1750 × 40 | 100°W – 50°W | 0°N – 70°N |
| `regp06` | 3-D, North Pacific (Kuroshio / W. Pacific) | 751 × 1501 × 40 | 150°E – 150°W | 10°N – 70°N |
| `regp07` | 3-D, NE Pacific (U.S. West Coast) | 626 × 1251 × 40 | 150°W – 100°W | 10°N – 60°N |
| `regp17` | 3-D, Arctic / high-latitude N. Pacific | 751 × 751 × 40 | 180° – 120°W | 60°N – 90°N |

- **Global 3-D volume is not published here:** only the four patches above carry full-depth fields. The global file is surface-only, and it excludes the polar caps poleward of ±80°.
- Only patches `01`, `06`, `07`, `17` are present on NOMADS (probed `regp01`–`regp20`); the gaps in numbering indicate these are selected patches from a larger internal set.

---

## Basic details
- **Model type:** Global coupled ocean + sea-ice forecast (deterministic), with data assimilation
- **System name:** Navy ESPC-D (Earth System Prediction Capability – Deterministic), V02
- **Core ocean model:** HYCOM **2.2.99** (HYbrid Coordinate Ocean Model)
- **Sea ice model:** CICE **5.1.2** (Community Ice CodE)
- **Experiment tag:** `expt_03.1` (`espc-d-031` in filenames)
- **Coupling:** Two-way NAVGEM ↔ HYCOM ↔ CICE (ESMF/NUOPC); one-way to WaveWatch III
- **Grid:** `glby0.08` — 1/12° global HYCOM grid; delivered on a regular lat-lon grid at 0.08° lon × 0.04° lat
- **Vertical levels (native):** HYCOM hybrid coordinate (isopycnal / sigma / z), ~41 layers
- **Vertical levels (distributed):** 40 fixed depth (z) levels, 0–5000 m (standard depths: 0, 2, 4, 6, 8, 10, 12, 15, 20, 25, 30, 35, 40, 45, 50, 60, 70, 80, 90, 100, 125, 150, 200, 250, 300, 350, 400, 500, 600, 700, 800, 900, 1000, 1250, 1500, 2000, 2500, 3000, 4000, 5000)
- **Vertical coordinate:** HYCOM hybrid — isopycnal in the stratified interior, terrain-following (sigma) in shallow water, z-level in the mixed layer
- **Forecast length:** 7 days (tau 0 to +168 h)
- **Update frequency:** Once daily
- **Production cycle:** 12Z (files stamped `2026072512`; `time_origin` 12:00 UTC)
- **Temporal output resolution:** 3-hourly (t0000, t0003, … t0168)
- **Latency / delivery:** notably delayed — the 12Z cycle is posted ~16+ hours later (e.g. the 25 July 12Z run had `created_on = 2026-07-26 04:29`). NOMADS pre-creates empty dated folders (`hycom.YYYYMMDD/`) ahead of population, so the newest *available* cycle typically lags the latest folder date by about a day.
- **Archive availability:** NOMADS holds only ~2–3 daily folders (rolling); long-term archive via NCEI / HYCOM Consortium data server
- **File format:** NetCDF-3 classic, individually gzip-compressed (`.nc.gz`); `Conventions = CF-1.0 NAVO_netcdf_v1.0`

---

## Forcing and assimilation
- **Atmospheric forcing / coupling:** NAVGEM (Navy Global Environmental Model), two-way coupled
- **Data assimilation:** NCODA (Navy Coupled Ocean Data Assimilation). `input_data_source` attribute lists NAVGEM plus satellite SSH (altimetry), SST, SSM/I (sea ice), and in-situ observations
- **Tidal forcing:** Astronomical tidal forcing applied in HYCOM (added in ESPC V2), producing internal tides reflected in `surf_el` / `steric_ssh`
- **Initial conditions:** NCODA analysis at the 12Z cycle

---

## What it provides
Variables verified from the live NetCDF.

### 3-D ocean fields (in `regp*` patches, 40 z-levels, 3-hourly)
- Water temperature (`water_temp`, °C)
- Salinity (`salinity`, psu)
- Eastward / northward water velocity (`water_u`, `water_v`, m/s)

### Surface / 2-D fields
- Water surface elevation (`surf_el`, m) and steric SSH (`steric_ssh`, m)
- Surface downward wind stress, eastward / northward (`surtx`, `surty`, Pa)
- In `sfc_u`: global surface (top-layer) `water_temp`, `salinity`, `water_u`, `water_v`, and `surf_el`

### Sea-ice fields (in `regp*` patches)
- Sea ice area fraction (`sic`)
- Sea ice thickness (`sih`, m)
- Sea ice velocity, eastward / northward (`siu`, `siv`, m/s)

---

## Data availability
- **Is the data free?** Yes (direct HTTP; no registration or key)
- **License:** Public domain — DoD **Distribution A**, "Approved for public release; distribution unlimited" (per each file's `distribution_statement`)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-3 classic, `NAVO_netcdf_v1.0` / CF-1.0 conventions, individually gzip-compressed (`.nc.gz` — one gzipped NetCDF per area per forecast hour; no tar bundling)
- **Delivery mechanism:** NOAA NOMADS (HTTP). No AWS Open Data mirror.
- **Official download location:**
  - Production: https://nomads.ncep.noaa.gov/pub/data/nccf/com/navo/prod/
  - Daily directories: `hycom.YYYYMMDD/` (date = cycle date; see latency note above)
  - NCEI catalog entry: https://www.ncei.noaa.gov/products/weather-climate-models/frnmoc-navy-global-hybrid-ocean
- **File naming:** `US058GCOM-OPSnce.espc-d-031-hycom008_fcst_{area}_{YYYYMMDDHH}_t{TAU}.nc.gz`
  - `{area}` ∈ {`sfc_u`, `regp01`, `regp06`, `regp07`, `regp17`}
  - `hycom008` = HYCOM 0.08° (1/12°); `espc-d-031` = ESPC-D expt 03.1; `US058GCOM` = NAVOCEANO product code
  - `{YYYYMMDDHH}` = cycle (e.g. `2026072512` = 25 Jul 2026 12Z); `{TAU}` = forecast hour, `0000`–`0168` in 3-hour steps
- **Approx. file sizes (gzipped):** `regp17` ~35 MB, `regp07` ~93 MB, `regp01` ~114 MB, `regp06` ~176 MB, `sfc_u` ~81 MB

---

## Version history
- **Current:** ESPC-D **V02** — HYCOM 2.2.99, CICE 5.1.2, expt 03.1 (from file metadata). Adds two-way NAVGEM–HYCOM–CICE coupling and astronomical tidal forcing relative to earlier standalone configurations.
- **7 November 2018:** GOFS 3.1 declared operational — 1/12° HYCOM (41 layers) two-way coupled to CICEv4, NCODA DA, NAVGEM forcing, 7-day forecasts. ESPC-D's ocean/ice lineage.
- **Earlier:** GOFS 3.0 (32-layer HYCOM); global NCOM before that. Atmospheric forcing moved NOGAPS → NAVGEM (NOGAPS retired 2013).

---

## Relationship to other ocean products

### NOAA counterpart (same ocean core)
- **[Global RTOFS](./rtofs-global.md)** — NOAA's run of HYCOM, forced by GFS instead of NAVGEM, with its own NCODA-derived initialization. RTOFS and Navy ESPC-D are the two operational runs of the Navy HYCOM lineage; ESPC-D is the coupled, NAVGEM-forced, tidally-forced Navy version.

### Regional systems nested inside this system
- **[Regional NCOM (AMSEAS / USEAST / etc.)](../../regional/us/ncom.md)** — takes lateral boundary conditions from the Navy global HYCOM system.

### Peer global ocean physics systems
- **[GLO12 (Copernicus Marine)](../france/glo12.md)** — NEMO-based 1/12° global peer.
- **[GIOPS (ECCC)](../canada/giops.md)** — NEMO-based, 1/4°, strong sea-ice physics.
- **BLUElink OceanMAPS** (Australia, ~1/10°).

### AI-based counterparts
- No operational AI counterpart specific to Navy ESPC-D is known as of this writing.

---

## Notes
- **In scope:** raw gridded NetCDF (3-D T/S/currents + sea ice on patches; global surface), open HTTP on NOMADS, DoD Distribution A. The `.nc.gz` is per-file gzip only.
- **Public subset:** the NOMADS product is not the full global 3-D ESPC-D field — global 3-D and additional regions are distributed through the HYCOM Consortium data server (`hycom.org`, `ESPC-D-V02` dataset), which is a separate access route.
- **Two grid labels:** files carry `grid_name = glby0.08` (the 1/12° delivered grid) but `source = HYCOM archive file, GLBz0.04`; the delivered fields measure 0.08° lon × 0.04° lat. Treat `glby0.08` as the authoritative delivered-product grid.
- **Cross-entry reconciliation:** the [NCOM entry](../../regional/us/ncom.md) currently cites its boundary-condition source as "Global HYCOM (GOFS 3.1)" per NCEI. Given ESPC-D V02 is the current operational Navy global system, that reference may warrant updating to ESPC-D — though whether the regional NCOM BC pipeline has switched from GOFS 3.1 output to ESPC-D is not confirmed in public docs (flagged, not asserted).

---

## Official documentation
- NOMADS Navy HYCOM data: https://nomads.ncep.noaa.gov/pub/data/nccf/com/navo/prod/
- HYCOM overview (hybrid coordinate): https://www.hycom.org/hycom/overview
- HYCOM Consortium data server (ESPC-D-V02, GOFS 3.1): https://www.hycom.org/dataserver
- NCEI — FNMOC Navy Global HYCOM: https://www.ncei.noaa.gov/products/weather-climate-models/frnmoc-navy-global-hybrid-ocean

### Key references
- Barton, N., et al. (2021). The Navy's Earth System Prediction Capability: A New Global Coupled Atmosphere–Ocean–Sea Ice Prediction System Designed for Daily to Subseasonal Forecasting. *Earth and Space Science*, 8, e2020EA001199.
- Metzger, E.J., et al. (2014). US Navy Operational Global Ocean and Arctic Ice Prediction Systems. *Oceanography*, 27(3), 32–43.
- Bleck, R. (2002). An oceanic general circulation model framed in hybrid isopycnic-Cartesian coordinates. *Ocean Modelling*, 4, 55–88.
