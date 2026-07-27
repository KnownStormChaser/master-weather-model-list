# NCOM (U.S. Navy Regional Navy Coastal Ocean Model)

## What this model is
The **Navy Coastal Ocean Model (NCOM)** is the U.S. Navy's regional coastal ocean physics forecast system. The product cataloged here is the set of **regional NCOM nests** distributed publicly through NOAA's NOMADS server: five fixed regional domains (`alaska`, `amseas`, `hawaii`, `socal`, `useast`) providing 3-D forecasts of temperature, salinity, and currents at ~3–4 km resolution.

Each nest is run within a **COAMPS** coupled framework: the ocean core is **NCOM version 4.6.8**, embedded in **COAMPS version v2021.5** (verified from the NetCDF `source` and `model_type` global attributes), with atmospheric forcing from a ~15 km application of the Navy's COAMPS atmospheric model. Data assimilation is provided by **NCODA** (Navy Coupled Ocean Data Assimilation), and lateral **boundary conditions come from the Navy's operational Global HYCOM (GOFS 3.1)**.

This is a *regional* system. The Navy's global ocean forecast is no longer NCOM — global NCOM was retired around 2013 and succeeded by Global HYCOM (GOFS), which NOAA also runs, with different atmospheric forcing, as [Global RTOFS](../../global/us/rtofs-global.md). The regional NCOM nests documented here are the surviving operational NCOM products, nested inside that global HYCOM parent.

> **Note on the NOMADS product description:** the text served alongside this dataset is badly out of date — it cites NOGAPS atmospheric forcing, "GOFS 2.6" boundary conditions, and 1/36° resolution. Live inspection and NCEI documentation show the current system uses COAMPS forcing, Global HYCOM (GOFS 3.1) boundary conditions, and 1/30° resolution.

---

## Who runs it
- **Production Unit:** Developed by the **U.S. Naval Research Laboratory (NRL)** (Barron et al., 2004, 2006); run operationally by the **U.S. Navy** — the NOMADS description attributes maintenance to the **Naval Oceanographic Office (NAVOCEANO)**, while NCEI catalogs the regional product under **FNMOC** (the exact operating center is stated inconsistently across sources)
- **Country:** United States
- **Public redistribution:** NOAA / NCEP, via NOMADS (long-term archive at NOAA NCEI)
- **Role in any larger system:** Regional downscaling nested inside the Navy Global HYCOM (GOFS 3.1) system; provides boundary/initial conditions to downstream coastal applications (e.g., CARICOOS FVCOM for Puerto Rico/USVI; NOAA STOFS-based coastal products)

---

## What area it covers
Five fixed regional nests, each on a regular lat-lon grid at **1/30° (0.0333°, ~3–4 km)** with **40 vertical levels** (verified from the live NetCDF; longitudes below converted from the files' 0–360°E convention):

| Nest | Domain (approx.) | Grid (lon × lat) | Longitude | Latitude |
|------|------------------|------------------|-----------|----------|
| `alaska` | Gulf of Alaska / NE Pacific / Bering | 1504 × 769 | 170.0°W – 119.9°W | 36.45°N – 62.05°N |
| `amseas` | American Seas: Gulf of Mexico, Caribbean, W. Atlantic | 1294 × 814 | 98.0°W – 54.9°W | 5.0°N – 32.1°N |
| `hawaii` | Hawaiian Islands | 424 × 424 | 166.0°W – 151.9°W | 15.0°N – 29.1°N |
| `socal` | Southern California / U.S. Southwest coast | 424 × 454 | 125.0°W – 110.9°W | 25.0°N – 40.1°N |
| `useast` | U.S. East Coast / W. Atlantic | 544 × 664 | 82.0°W – 63.9°W | 20.0°N – 42.1°N |

- **Special masked or excluded regions:** land masked per nest bathymetry (`watdep` field included in each file)

---

## Basic details
- **Model type:** Regional ocean physics forecast (deterministic), nested in a global parent, with data assimilation
- **Core ocean model:** NCOM (Navy Coastal Ocean Model) **version 4.6.8**
- **Framework:** COAMPS **version v2021.5** (Coupled Ocean/Atmosphere Mesoscale Prediction System)
- **System name:** Regional NCOM (COAMPS-NCOM nests)
- **Horizontal resolution:** 1/30° (~3–4 km) on all five nests (verified)
- **Vertical levels (distributed product):** 40 fixed depth (z) levels, 0 m to 5000 m (standard depths: 0, 2, 4, 6, 8, 10, 12, 15, 20, 25, 30, 35, 40, 45, 50, 60, 70, 80, 90, 100, 125, 150, 200, 250, 300, 350, 400, 500, 600, 700, 800, 900, 1000, 1250, 1500, 2000, 2500, 3000, 4000, 5000)
- **Vertical coordinate:** NCOM's native coordinate is **hybrid sigma-Z** (terrain-following sigma near the surface, fixed z below); the distributed NetCDF is interpolated to the 40 fixed z-levels above
- **Forecast length:** to **tau +96 h** (4 days); commonly described as a 1-day nowcast plus ~3-day forecast
- **Update frequency:** Once daily
- **Production cycles:** 00Z (files stamped `t00`, `time_origin` 00:00 UTC)
- **Target delivery time:** nest-dependent; on 2026-07-26, nests posted between ~08Z (`socal`, `useast`, `alaska`) and ~12Z (`amseas`)
- **Temporal output resolution:** 3-hourly (tau 0, 3, 6, … 96)
- **Archive availability:** NOMADS holds a ~7-day rolling window (`ncom.YYYYMMDD/`); long-term archive at NOAA NCEI
- **File format:** NetCDF-3 classic (64-bit offset); `Conventions = NAVO_netcdf_v1.1`
- **Bathymetry source:** per-nest bathymetry (distributed as the `watdep` variable)

---

## Forcing
- **Atmospheric forcing:** Navy **COAMPS** atmospheric model (~15 km) — surface wind stress, atmospheric pressure, and heat/solar fluxes are carried as fields in each file (`surf_wnd_stress_gridx/gridy`, `surf_atm_press`, `surf_temp_flux`, `surf_solar_flux`)
- **Lateral boundary conditions:** Navy Global **HYCOM (GOFS 3.1)**, 1/12° (since April 2013; prior to that, Global NCOM)
- **Data assimilation:** **NCODA** — assimilates satellite SST and altimetry plus in-situ temperature/salinity profiles, using Improved Synthetic Ocean Profiles to project surface information downward
- **Tidal forcing:** Not indicated in the distributed fields (TBD)
- **Initial conditions:** NCODA analysis at the 00Z cycle

---

## Coupling
- **Framework:** Run inside COAMPS v2021.5. The distributed fields include COAMPS-provided surface atmospheric forcing over each ocean domain, consistent with COAMPS→NCOM atmospheric forcing. Whether any given nest runs two-way air–sea coupled vs. one-way forced is not stated in the public metadata (TBD).
- **Parent ocean model:** One-way nested in Global HYCOM (GOFS 3.1) via lateral boundary conditions.

---

## What it provides
Variables verified from the live NetCDF (`socal` nest, tau 0). All 3-D fields are on the 40 fixed z-levels:

### 3-D ocean fields (3-hourly)
- Water temperature (`water_temp`, °C)
- Salinity (`salinity`, psu)
- Eastward / northward water velocity (`water_u`, `water_v`, m/s)
- Vertical water velocity (`water_w`, m/s)

### Surface / 2-D fields (3-hourly)
- Water surface elevation / SSH (`surf_el`, m)
- Barotropic transport, eastward / northward (`water_baro_u`, `water_baro_v`, m²/s)
- Surface atmospheric pressure (`surf_atm_press`, mbar)
- Surface wind stress, eastward / northward (`surf_wnd_stress_gridx`, `surf_wnd_stress_gridy`, Pa)
- Surface roughness (`surf_roughness`, m)
- Surface temperature flux (`surf_temp_flux`, °C·m/s)
- Surface shortwave/solar flux (`surf_solar_flux`, °C·m/s)

### Static fields
- Water depth / bathymetry (`watdep`, m)
- Longitude / latitude coordinate arrays; depth levels

> Note: these nests carry **ocean physics only** — no sea ice fields are included (in contrast to Global RTOFS).

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). Each NetCDF file carries `distribution_statement = "Approved for Public Release; distribution unlimited."`
- **Is the data downloadable?** Yes (direct HTTP; no registration or key)
- **Data formats:** NetCDF-3 classic, `NAVO_netcdf_v1.1` conventions, **packaged in gzip-compressed tar archives (`.tgz`)**
- **Delivery mechanism:** NOAA NOMADS (HTTP). No AWS Open Data mirror exists for NCOM (bucket probe returned no `noaa-ncom-*` bucket).
- **Official download location:**
  - Production: https://nomads.ncep.noaa.gov/pub/data/nccf/com/ncom/prod/
  - Daily directories: `ncom.YYYYMMDD/` (~7-day rolling window)
  - Long-term archive (NCEI): https://www.ncei.noaa.gov/products/weather-climate-models/fnmoc-regional-navy-coastal-ocean
- **File (tarball) naming:** `{nest}_u_ocn_ncout_grid1_{YYYYMMDDHH}_t{START}-{END}.tgz`
  - `{nest}` ∈ {`alaska`, `amseas`, `hawaii`, `socal`, `useast`}; `{YYYYMMDDHH}` = init (e.g. `2026072600`)
  - Four tarballs per nest per run, splitting the forecast into ~24 h blocks: `t0000-0024`, `t0025-0048`, `t0049-0072`, `t0073-0096`
- **Contents of each tarball:** the 3-hourly NetCDF files for that block, named `coamps_ncom_{nest}_u_1_{YYYYMMDDHH}_{HHMMSS}.nc` (e.g. `coamps_ncom_socal_u_1_2026072600_00030000.nc` = tau +3 h). Extract with `tar -xzf <file>.tgz`.
- **Approx. per-tarball size:** ~180 MB (`socal`) to ~1.8 GB (`alaska`), varying by nest domain size

---

## Version history
- **Current (as of the 2026-07-26 run):** NCOM **v4.6.8** within COAMPS **v2021.5** (from file metadata). No public Service Change Notice stream tracks these Navy regional nests the way NCEP tracks its own models; version is best read from the NetCDF `source`/`model_type` attributes.
- **April 2013:** Boundary-condition source switched from Global NCOM to **Global HYCOM** (1/12°); horizontal resolution of the distributed regional datasets changed at the same time (per NCEI).
- **November 2009:** Regional NCOM output moved to **40 vertical levels** (earlier output had 34) — per NCEI.

---

## Relationship to other ocean products

### Parent global system
- **Navy Global HYCOM (GOFS 3.1)** — provides lateral boundary conditions to these nests; the successor to the retired Global NCOM. *(Repository entry pending — the planned HYCOM entry.)*

### Peer / sibling systems
- **[Global RTOFS](../../global/us/rtofs-global.md)** — NOAA's run of the same Navy Global HYCOM core, with different (GFS) atmospheric forcing. RTOFS is the global-scale counterpart; NCOM here is the Navy's high-resolution regional coastal complement.
- **Other regional coastal ocean systems** over overlapping U.S. domains (e.g. NOAA's regional operational forecast systems) differ in model core and operator.

### Regional models nested inside / downstream of this system
- **CARICOOS FVCOM (PRVI)** and other coastal observing-system models take boundary conditions from the `amseas` (American Seas) nest.

### AI-based counterparts
- No operational AI counterpart specific to regional NCOM is known as of this writing.

---

## Notes
- **In-scope determination:** although distributed as `.tgz` tarballs, the underlying data is raw gridded NetCDF (3-D T/S/currents on fixed z-levels), served over a permanent open HTTP channel under a public-domain statement — squarely in scope. The tarball is packaging only.
- **Regional, not global:** this entry is deliberately scoped to the surviving *regional* NCOM nests. Do not conflate with the historical Global NCOM (retired ~2013) or with Navy Global HYCOM / GOFS.
- **Stale NOMADS description:** see the callout at the top — NOGAPS/GOFS-2.6/1-36° references in the served description are obsolete.
- **Per-nest verification:** grid dimensions, domains, and resolution above were read from the live NetCDF for all five nests; the full variable list was read from the `socal` nest and is expected to be identical across nests (the file schema is shared), but per-nest variable parity was not exhaustively re-verified.

---

## Official documentation
- NOMADS NCOM data: https://nomads.ncep.noaa.gov/pub/data/nccf/com/ncom/prod/
- NCEI — FNMOC Regional Navy Coastal Ocean Model: https://www.ncei.noaa.gov/products/weather-climate-models/fnmoc-regional-navy-coastal-ocean
- NCEI — FNMOC Navy Global HYCOM (parent system): https://www.ncei.noaa.gov/products/weather-climate-models/frnmoc-navy-global-hybrid-ocean

### Key references
- Barron, C.N., A.B. Kara, H.E. Hurlburt, C. Rowley, and L.F. Smedstad, 2004: Sea surface height predictions from the Global Navy Coastal Ocean Model (NCOM) during 1998–2001. *J. Atmos. Oceanic Technol.*, 21(12), 1876–1894.
- Barron, C.N., A.B. Kara, P.J. Martin, R.C. Rhodes, and L.F. Smedstad, 2006: Formulation, implementation and examination of vertical coordinate choices in the global Navy Coastal Ocean Model (NCOM). *Ocean Modelling*, 11, 347–375. doi:10.1016/j.ocemod.2005.01.004
