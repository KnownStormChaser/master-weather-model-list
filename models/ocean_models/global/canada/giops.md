# GIOPS (Global Ice-Ocean Prediction System)

## What this model is
The **Global Ice-Ocean Prediction System (GIOPS)** is the operational global ocean and sea ice prediction system of **Environment and Climate Change Canada (ECCC)**, run at the **Canadian Centre for Meteorological and Environmental Prediction (CCMEP)**. It produces a daily global ice-ocean analysis and 10-day forecasts of temperature, salinity, currents, sea surface height, and a broad set of sea ice fields, distributed as CF-compliant NetCDF on two grids through the MSC Datamart.

**GIOPS is two systems sharing one name, and the distributed files make this explicit.** The *analysis* component is a SAM2 ocean-ice assimilation system on its own 3.x version line; the *forecast* component is the sea ice-ocean component of the [GDPS](../../../nwp_models/global/canada/gem-global.md) coupled run and is versioned with GDPS. Files from a single run carry both stamps: `Anal000` files declare `Global Ice Ocean Prediction System: version 3.4.0`, while every `P003`–`P240` forecast file declares `version 10.0.0`. The GDPS technical specification confirms the architecture with a section headed *"GDPS version 10.0.0 – Forecast component (sea ice-ocean)"*, whose model initialization line reads "Coming from the global ice-ocean prediction system (GIOPS) version scheme 3.5.0."

This matters beyond bookkeeping. Because the GIOPS forecast runs inside GDPS, and **GDPS v10.0.0 (26 May 2026) made the operational atmosphere a hybrid physics-AI system spectrally nudged toward [GEML](../../../nwp_models/global/canada/gdps-geml.md)**, the GIOPS ocean forecast has since been integrated against an AI-influenced atmosphere. The GIOPS changelog page does not mention this — its most recent entry is 14 April 2026 (v3.6.0, HPC migration). See *Notes*.

Among the three major operational global ocean physics forecasts — GIOPS, Mercator's [GLO12](../france/glo12.md), and NOAA's [Global RTOFS](../us/rtofs-global.md) — GIOPS is the coarsest at 1/4°, but is the only one whose ocean is two-way coupled to its operator's operational atmospheric NWP model, and the only one shipping a dedicated Northern Hemisphere polar stereographic product alongside a global grid.

---

## Who runs it
- **Production Unit:** Canadian Centre for Meteorological and Environmental Prediction (CCMEP), Meteorological Service of Canada (MSC)
- **Country:** Canada
- **Programme:** MSC Open Data
- **Role in any larger system:**
  - Supplies the ice-ocean initial state for the coupled [GDPS](../../../nwp_models/global/canada/gem-global.md) forecast, and its own forecast component *is* the GDPS sea ice-ocean component
  - Initializes [RIOPS](../../regional/canada/riops.md), which in turn drives [CIOPS](../../regional/canada/ciops.md)
  - Per the v3.5.0 factsheet, GIOPS analyses also initialize the Global Ensemble Prediction System (GEPS) and the Canadian Seasonal to Inter-annual Prediction System (CanSIPS)
- **Contact stamped in files:** `production-info@ec.gc.ca`

---

## What area it covers
- **Coverage:** Global ocean (model); distribution is split between a global grid and a Northern Hemisphere grid
- **Native model grid:** ORCA025 — global tripolar Arakawa C-grid at 1/4°. The factsheet gives 1442 × 1021 points. **The native grid is not distributed**; only the two interpolated grids below are public.
- **Vertical levels:** 50 z-levels

### Distributed grid 1 — global lat-lon (`latlon0.2x0.2`)
- **Verified dimensions:** 1800 × 850, regular 0.2° × 0.2°
- **Longitude:** 0.0° to 359.8° — no duplicated wrap column at the seam
- **Latitude:** −80.0° to +89.8°, i.e. the grid stops at 80°S and does not reach the North Pole row
- **Grid mapping:** `latitude_longitude`, spherical earth, `earth_radius = 6371229.0` m

### Distributed grid 2 — polar stereographic (`ps5km60N`)
- **Verified dimensions:** 1770 × 1610 at exactly 5000 m spacing; `xc` 0 → 8,845,000 m, `yc` 0 → 8,045,000 m
- **Verified extent:** corner latitudes 30.56°, 28.92°, 43.91°, 41.47° — the grid reaches to **~28.9°N**, spanning essentially the whole Northern Hemisphere north of the subtropics
- **Projection parameters (from the `polar_stereographic` grid-mapping variable):** `latitude_of_projection_origin = 90.0`, `straight_vertical_longitude_from_pole = −100.0`, `scale_factor_at_projection_origin = 0.9330124` (true scale at 60°N), `false_easting = 4245000.0`, `false_northing = 5295000.0`, `earth_radius = 6371229.0`
- Files carry 2D auxiliary `latitude` / `longitude` coordinate arrays. These dominate file size — see *Notes*.

> **ECCC's own description understates this grid.** Both the GIOPS readme and the Datamart page describe it as covering "the Arctic Ocean and the neighbouring sub-polar seas." It is a Northern Hemisphere grid reaching into the subtropics. Users who skipped it because they work at mid-latitudes should reconsider.

### Masked regions
Land and below-seafloor points use `_FillValue = 1e+20`. Ocean fields are valid over **70.48%** of the lat-lon grid. Sea ice fields are masked far more aggressively and are **not zero-filled over open water** — see *Notes*.

---

## Basic details
- **Model type:** Global deterministic ocean physics and sea ice; analysis with data assimilation plus coupled forecast
- **Core ocean model:** NEMO 3.6 (Madec et al., 1998; Madec and NEMO team, 2008)
- **Sea ice model:** CICE 6.2.0 (Hunke et al., 2021), upgraded from CICE 4.0 in v3.5.0
- **System name:** GIOPS. Analysis component documented at v3.6.0; forecast component versioned with GDPS (currently 10.0.0)
- **Horizontal resolution:** 1/4° native (ORCA025); distributed at 0.2° global and 5 km polar stereographic
- **Vertical levels:** 50 z-levels
- **Vertical coordinate:** z-level (fixed depth), `positive = down`
- **Verified depth levels (m):** 0.494025, 1.54138, 2.64567, 3.81949, 5.07822, 6.44061, 7.92956, 9.573, 11.405, 13.4671, 15.8101, 18.4956, 21.5988, 25.2114, 29.4447, 34.4342, 40.3441, 47.3737, 55.7643, 65.8073, 77.8539, 92.3261, 109.729, 130.666, 155.851, 186.126, 222.475, 266.04, 318.127, 380.213, 453.938, 541.089, 643.567, 763.333, 902.339, 1062.44, 1245.29, 1452.25, 1684.28, 1941.89, 2225.08, 2533.34, 2865.7, 3220.82, 3597.03, 3992.48, 4405.22, 4833.29, 5274.78, **5727.92**
- **Forecast length:** 240 h (10 days), both cycles
- **Update frequency:** 2× daily
- **Production cycles:** 00 and 12 UTC. **Only the 00Z cycle carries an analysis**; the 12Z tree has no hour-`000` directory at all.
- **Temporal output resolution (verified):** 2D fields are **3-hourly means** at steps 003–240 (80 steps); 3D fields are **24-hourly means** at steps 024–240 (10 steps). Validity time is stamped at the **end** of the averaging interval — a `P003` file from the 00Z run reports `time = 03:00 UTC`.
- **Numerical technique (forecast component, per GDPS spec):** primitive equations, finite differences, Arakawa C-grid; explicit leapfrog with a non-linear free surface and barotropic sub-time-stepping; **450 s time step**
- **Horizontal diffusion:** bi-Laplacian on momentum along geopotential coordinates; Laplacian on tracers along iso-neutral surfaces
- **Vertical mixing:** TKE scheme (Gaspar et al., 1990; Blanke and Delecluse, 1993)
- **Bathymetry source:** **Etopo2** (stated in the GDPS 10.0.0 technical specification)
- **Archive availability:** rolling 30 days on Datamart — see *Data availability*

---

## Forcing
- **Atmospheric forcing (analysis cycle):** temperature, winds, radiative fluxes and humidity from GDPS, taken from the **delayed-mode (G2) GDPS analysis** rather than forecast fields — a choice introduced in v3.3.0 (December 2021) that gives the assimilation cycle the best-quality atmospheric state. *The GIOPS 3.5.0 technical specification names GDPS **v9.0.0** here; GDPS has since moved to 9.1.0 and then 10.0.0, so this line is stale in the published document.*
- **Atmospheric coupling (forecast):** not a forcing at all — the forecast is two-way coupled. See *Coupling*.
- **River runoff:** climatological (NEMO/ORCA025 standard configuration). Not documented in the GIOPS or GDPS specifications — **TBD**, confirm before citing.
- **Tidal forcing:** none in the global system. Explicit tides are handled in [RIOPS](../../regional/canada/riops.md).
- **Initial conditions:** the coupled GDPS forecast is initialized from the GIOPS analysis (version scheme 3.5.0 per the GDPS spec). **What initializes the 12Z forecast is not documented anywhere** — the analysis is 00Z-daily only, so the 12Z run is presumably a continuation of the 00Z integration, but this is an inference. **TBD.**

---

## Coupling

### Atmosphere (GDPS) — two-way
Since v2.3 (November 2017), GIOPS is two-way coupled with GDPS. The GDPS 10.0.0 technical specification gives the mechanics:

- **Coupling frequency:** every ocean time step (450 s)
- **Coupler:** ECCC's in-house communicator **GOSSIP**
- **Field flow:** air-ice and air-ocean turbulent fluxes of momentum, sensible and latent heat are computed *inside NEMO* using the same bulk formulas as GEM, aggregated onto the ocean grid, then passed to GEM. Sea surface temperature, sea ice concentration, snow depth over sea ice, and sea ice thickness flow from the ocean side into the GDPS atmospheric forecast.

This is operationally uncommon — GLO12 and RTOFS both take one-way atmospheric forcing.

### Sea ice (CICE 6.2.0) — online
- **Radiation:** Delta-Eddington multiple scattering (Briegleb and Light, 2007), with `R_ice = 2.0`, `R_pnd = 2.0`, `R_snw = 2.0`
- **Conductivity:** "bubbly" brine-inclusive scheme (Pringle et al., 2007)
- **Dynamics:** Elastic-Viscous-Plastic rheology (Hunke, 2001)
- **Ice strength:** P\* = 22.5 kN/m², C\* = 15
- **Air-ice roughness:** `iceruf` = 0.54 mm

### Waves
No wave coupling in the global system.

---

## Data assimilation

Applies to the **analysis component** only. The forecast component runs free within the coupled GDPS integration.

- **DA scheme:** SAM2 (Système d'Assimilation Mercator) — reduced-order Kalman filter using a SEEK (Singular Evolutive Extended Kalman) formulation (Pham et al., 1998), adapted from Mercator Ocean and shared in lineage with [GLO12](../france/glo12.md)
- **Update cycle:** two weekly analyses (delayed-mode and real-time) producing an analysis valid on Wednesday, plus a **daily analysis update at 00 UTC**
- **Increment application:** Incremental Analysis Update (IAU) applied over one day; 4D-IAU introduced in v2.1
- **Background-error covariances:** modelled 3D anomalies derived from a multi-year hindcast simulation (Lellouche et al., 2013)
- **Mean Dynamic Topography:** new MDT field as of v3.5.0
- **In-situ quality control:** DFOQC (added v3.4.1)

### Assimilated observations
- **Sea surface temperature:** the CCMEP gridded SST analysis at 0.1°, which itself ingests AVHRR (MetOp-B, MetOp-C), VIIRS (Suomi NPP, NOAA-20), AMSR2 (GCOM-W1), and in-situ buoy, drifter and ship data
- **Sea level anomaly:** six altimeters — SARAL/AltiKa, CryoSat-2N, Jason-3N, Sentinel-3A, Sentinel-3B, Sentinel-6A-HR. Jason-3N and Sentinel-6A-HR were added in v3.5.0, roughly doubling the assimilated altimetric observation count.
- **Temperature and salinity profiles:** Argo, drifters, bathythermographs, gliders, ships of opportunity, buoys and moorings
- **Sea ice concentration:** the CMC global ice concentration analysis (3D-Var FGAT at 10 km resolution), per the GDPS specification

> **The two specifications disagree on in-situ and altimetry sourcing.** The GIOPS 3.5.0 specification says profiles are obtained from **CMEMS** and SLA from the six named altimeters. The GDPS 10.0.0 specification describes the same initialization as "SAM2 ingesting SLA from **AVISO**, in situ temperature and salinity profiles from **CLS**." These are plausibly the same data along different described routes, but the documents are not reconcilable as written. Flagged, not resolved.

---

## What it provides

Seventeen 2D variables and four 3D variables, identical on both distributed grids (verified by diff of the two file lists at step 003).

### 3D ocean fields (4 variables, `depth_all`, 50 levels)
| Variable | `nomvar` | Units in file | Standard name |
|---|---|---|---|
| `votemper` | `TM` | `Kelvin` | `sea_water_potential_temperature` |
| `vosaline` | `SALW` | `1e-3` | `sea_water_salinity` |
| `vozocrtx` | `UUW` | `m s-1` | `sea_water_x_velocity` |
| `vomecrty` | `VVW` | `m s-1` | `sea_water_y_velocity` |

**No vertical velocity is distributed.** The GDPS specification lists vertical velocity as a *derived* variable of the ocean model, but it is not written to any public file.

### Surface and near-surface fields (2D)
The same four ocean variables are also issued as 2D files at `depth_0.5` (the first model level, 0.494 m), plus:

| Variable | `nomvar` | Units in file | Notes |
|---|---|---|---|
| `sossheig` | `SSH` | `m` | `sea_surface_height_above_geoid` — reference frame is the geoid, stated explicitly in the standard name |
| `sokaraml` | `MLW` | `m` | Mixed layer depth, **density criterion** |
| `somixhgt` | `MLTW` | `m` | **Turbocline depth** — a different diagnostic, not a duplicate of `sokaraml` |

### Sea ice fields (2D)
| Variable | `nomvar` | Units in file | Description |
|---|---|---|---|
| `iiceconc` | `GL` | `1` | Sea ice area fraction |
| `iicevol` | `GE` | `m` | Sea ice **volume per unit grid cell area** — not ice thickness |
| `isnowvol` | `SDV` | `cm` | Snow volume per unit grid cell area — note the centimetre unit |
| `itzocrtx` | `UUI` | `m s-1` | Sea ice x velocity |
| `itmecrty` | `VVI` | `m s-1` | Sea ice y velocity |
| `iicesurftemp` | `TMI` | `Kelvin` | Surface temperature of snow over sea ice, or of bare ice |
| `iicestrength` | `STGI` | `N m-1` | Depth-integrated compressive ice strength |
| `iicepressure` | `SIII` | `N m-1` | Depth-integrated internal ice pressure |
| `iicedivergence` | `DIVI` | `%/day` | Sea ice divergence |
| `iiceshear` | `SHRI` | `%/day` | Sea ice shear strain rate |

The ice deformation and mechanics set (divergence, shear, strength, internal pressure) is unusually rich for a global operational product — most global systems ship concentration and thickness only.

### Analysis-hour contents (00Z, hour `000`)
A deliberately reduced set, `cell_methods = time: point`:
- **2D:** `iiceconc` and `sossheig` only
- **3D:** all four (`votemper`, `vosaline`, `vozocrtx`, `vomecrty`)

ECCC's guidance is to take the surface level of the 3D analysis files to recover 2D analysis temperature, salinity and currents.

### Static fields
None distributed. No bathymetry, land-sea mask, or grid cell dimension files are published — the land-sea mask must be inferred from the `_FillValue` pattern.

---

## Data availability

- **Is the data free?** Yes — no registration, no API key, direct HTTPS
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required. Suggested attribution: "Data Source: Environment and Climate Change Canada." https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded only
- **Data formats:** NetCDF-4 classic model (HDF5 container), `Conventions = CF-1.6`, **zlib level 1 with shuffle**. Chunking: lat-lon 2D `[1, 425, 900]`, polar stereographic 2D `[1, 805, 885]`, lat-lon 3D `[1, 13, 284, 600]`. Time encoded as seconds since 1950-01-01 00:00:00, gregorian calendar.
- **Official download location:**
  - Current day: https://dd.weather.gc.ca/today/model_giops/netcdf/
  - Dated archive: `https://dd.weather.gc.ca/{YYYYMMDD}/WXO-DD/model_giops/netcdf/{grid}/{nd}/{HH}/{hhh}/`
  - where `{grid}` is `lat_lon` or `polar_stereographic`, `{nd}` is `2d` or `3d`, `{HH}` is `00` or `12`, and `{hhh}` is `000`–`240` step 3 for `2d` and step 24 for `3d`
- **File naming:** `CMC_giops_{Variable}_{LevelType}_{Level}_{Projection}{Resolution}_{TimeMean}_{YYYYMMDDHH}_{FileType}{hhh}.nc`
  - Forecast: `CMC_giops_votemper_depth_0.5_latlon0.2x0.2_3h-mean_2026080800_P003.nc`
  - Analysis: `CMC_giops_iiceconc_sfc_0_latlon0.2x0.2_2026080800_Anal000.nc` — the `TimeMean` token is absent entirely
  - Polar stereographic: `CMC_giops_vomecrty_depth_all_ps5km60N_24h-mean_2026080800_P024.nc`
- **Files per run (live-confirmed on the 2026-08-08 cycles):**
  - **00Z: 2,812 files, 22.14 GiB** — lat-lon 2D 1,362 (1.19 GiB), lat-lon 3D 44 (3.25 GiB), polar stereographic 2D 1,362 (13.50 GiB), polar stereographic 3D 44 (4.21 GiB)
  - **12Z: 2,800 files, 21.04 GiB** — identical structure minus the twelve hour-`000` analysis files
  - **~43 GiB per day**
- **File size:** lat-lon 2D 0.22–2.5 MiB; lat-lon 3D 42–102 MiB; polar stereographic 2D 9.2–12 MiB; polar stereographic 3D 57–127 MiB. Analysis files are consistently *larger* than forecast files of the same variable (lat-lon 3D `votemper`: 87 MiB analysis vs 42 MiB forecast) because instantaneous fields compress less well than time means.
- **Retention:** dated directories persist **30 days** — live-probed 2026-08-09: `20260710` present, `20260709` returns 404
- **Publication latency (five-day sample, 2026-08-03 to 2026-08-08):**
  - 00Z analysis files ~**T+2h07m to T+2h11m**
  - 00Z forecast written as a single burst completing ~**T+4h46m to T+5h04m** (2,800 files inside ~4 minutes)
  - 12Z forecast completing ~**T+4h47m to T+4h59m**
  - One outlier: on 2026-08-05 the analysis files were written at 05:36 UTC, roughly 3.5 h late
- **Push notification:** available via AMQP (MSC Datamart sr3/sarracenia)
- **Other access:** MSC GeoMet serves **168 GIOPS layers** as WMS/WCS, named by `nomvar` (`OCEAN.GIOPS.2D_GL`, `OCEAN.GIOPS.3D_SALW_0000`, …), grouped as "Global Ice Ocean Prediction System in 2/3 dimensions [24 km]". WCS can return raw coverage subsets, but the Datamart NetCDF is the canonical raw distribution. Also surfaced through MSC AniMet as a visualization service.
- **Discovery metadata:** https://open.canada.ca/data/en/dataset/dc3a7022-95e8-45a7-bf63-3d45b6cda0dc

---

## Version history

Two version lines run in parallel. The changelog below is the **analysis** line, which is what ECCC's GIOPS changelog documents. The forecast component's version history is the [GDPS](../../../nwp_models/global/canada/gem-global.md) history.

### April 14, 2026 — GIOPS v3.6.0 (current documented version)
- Migration to ECCC's new High Performance Computing infrastructure
- Computational only; no scientific change to formulation, DA, or outputs

### May 26, 2026 — forecast component moves to GDPS 10.0.0 (undocumented in the GIOPS changelog)
- Not a GIOPS changelog entry, but the forecast files' version stamp changes with it
- GDPS 10.0.0 introduced hybrid physics-AI forecasting via spectral nudging toward [GEML](../../../nwp_models/global/canada/gdps-geml.md); the coupled ocean integrates against that atmosphere

### June 11, 2024 — GIOPS v3.5.0
- Sea ice model upgraded from CICE 4.0 to **CICE 6.2.0**
- Delta-Eddington radiation scheme for sea ice (Briegleb and Light, 2007)
- "Bubbly" thermal conductivity scheme (Pringle et al., 2007)
- New Mean Dynamic Topography field
- Three new altimeters activated (CryoSat-2N, Jason-3N, Sentinel-6), roughly doubling assimilated altimetric observations

### November 28, 2023 — GIOPS v3.4.1
- Added DFOQC quality control for in-situ observations used in data assimilation

### June 28, 2022 — GIOPS v3.4.0
- HPC infrastructure migration (computational only)

### December 1, 2021 — GIOPS v3.3.0
- Diurnal cycle included in atmospheric forcing
- Ice strength parameter updated; ice roughness adjusted to match the GEM value
- Atmospheric forcing switched from the GDPS forecast run to the assimilation (delayed-mode G2) run
- New monitoring system

### January 21, 2020 — GIOPS v3.2.1
- HPC infrastructure migration (computational only)

### July 3, 2019 — GIOPS v3.0.0
- Updated to SAM2
- New SST and sea ice analyses at 0.1° (from 0.2°)

### November 1, 2017 — GIOPS v2.3
- **Two-way coupling with GDPS introduced**

### June 21, 2016 — GIOPS v2.1
- New assimilation code based on Mercator-Océan's 2015 SAM
- 4D Incremental Analysis Update introduced
- Improved Mean Dynamic Topography; bogus observations under sea ice; refined CMC SST and ice analyses

### August 20, 2015 — GIOPS v1.1.1
- Declared operational by CMC

---

## Notes

- **Sea ice fields are masked, not zero-filled.** Over the lat-lon grid, ocean variables are valid at 70.48% of points but `iiceconc` is valid at only **14.00%**, and the minimum valid concentration is **0.01001** — everything below 1% ice cover is written as `_FillValue`, not zero. All ten ice variables share the concentration mask exactly, and the deformation fields are masked tighter still: `iicedivergence` valid at 8.71%, `iiceshear` at 5.02%. On the polar stereographic grid `iiceconc` is valid at 10.82%. **Code that assumes open water reads as zero will silently ingest 1e+20.** The ice mask is a strict subset of the ocean mask.

- **Polar stereographic files are ~93% coordinate overhead.** A 9.6 MiB `ps5km60N` 2D file stores 4.42 MiB of `latitude` and 4.52 MiB of `longitude` auxiliary arrays against a 0.66 MiB data payload. The same two arrays are re-shipped in all 1,362 polar stereographic 2D files in a run — roughly **12 of the 13.5 GiB** in that tree is redundant. Users mirroring this product should extract the coordinate arrays once and consider stripping them, or work from the lat-lon tree, which is ~11× smaller for the same 2D variable set.

- **The `title` global attribute contradicts `cell_methods` in every forecast file.** Forecast files are titled `Instantaneous sea ice and ocean forecast fields` (2D) or `Instantaneous ice and ocean forecast fields` (3D) while carrying `cell_methods = time: mean (interval: 3 hours)` or `(interval: 24 hours)`. The `cell_methods` attribute is correct and matches the filename token and the Datamart documentation; the title is wrong. Analysis files are titled `Ice ocean analysis fields` and correctly carry `time: point`.

- **Neither in-file version stamp matches the documented version.** Analysis files have stamped `3.4.0` continuously across the entire 30-day retention window (verified 2026-07-12 through 2026-08-08, both grids, 2D and 3D) even though the documented analysis version has been 3.5.0 since June 2024 and 3.6.0 since April 2026 — the stamp is two releases stale. Forecast files stamp `10.0.0` / `GIOPS_10.0.0_F3`, tracking GDPS rather than the GIOPS line. **There is no file-level record of the actual operational GIOPS analysis version**; archivists should record it externally.

- **Temperatures are in Kelvin, and the file units disagree with ECCC's own variable table.** The Datamart variable list gives `PSU` for salinity, `Fraction` for ice concentration and `K` for temperatures; the files use `1e-3`, `1` and `Kelvin` respectively. The file values are CF-conformant; the published table is looser. Nothing here is wrong numerically, but string-matching on units across the two sources will fail.

- **Validity time is the end of the averaging interval.** A `P003` file from the 00Z run covers 00–03 UTC and is stamped 03:00 UTC. This is documented, but it is the opposite convention from several other products in this catalog and is easy to get wrong by one interval.

- **`iiceshear` appears to be clipped at its declared ceiling.** 123 of 76,812 valid points sit at exactly `2.0` %/day, the field's `valid_max`. Consistent with clipping rather than coincidence, though not confirmed against ECCC documentation.

- **The published depth range is wrong at the bottom.** The GIOPS 3.5.0 technical specification states "50 levels with depths from 0.5 m to 5500 m." The deepest distributed level is **5727.92 m**, and the shallowest is 0.494025 m. The Datamart page lists the correct 50 values.

- **The Datamart "List of variables" table is JavaScript-loaded and invisible to scrapers.** It is fetched at render time from `https://eccc-msc.github.io/open-data/assets/csv/GIOPS_Variables-List_en.csv`. Fetch the CSV directly. (Same pattern as [RESPS](../../../storm_surge_models/regional/canada/resps.md) and [GDSPS](../../../storm_surge_models/global/canada/gdsps.md).)

- **The linked "current version" technical specification is stale.** `tech_specifications_GIOPS_e.pdf`, linked from the GIOPS readme as the current specification, still serves the **v3.5.0 analysis** document dated 2024 — it predates v3.6.0 and, by title, only ever covered the analysis component. For the forecast component, the GDPS technical specification is the authoritative document.

- **The 12Z cycle is under-documented.** The Datamart page correctly lists `[00,12]` but says nothing about the asymmetry: the 12Z tree has no hour-`000` directory, so there is no 12Z analysis and no 12Z instantaneous field of any variable. Anyone building a continuous analysis time series gets one sample per day, not two.

- **AI relationship.** GIOPS is not itself an AI system, but since GDPS 10.0.0 its forecast component has been coupled to a GEML-nudged atmosphere, making it a downstream AI-influenced product. [`AI_MODELS.md`](../../../../AI_MODELS.md) currently indexes GEML and GDPS but not the ocean systems that inherit the coupling. **Whether GIOPS, [RIOPS](../../regional/canada/riops.md) and [CIOPS](../../regional/canada/ciops.md) warrant entries in that index is an open scope question** — they are physics models whose forcing is partly AI-derived, which is a category the index does not currently have. Flagged for decision rather than resolved here.

- **Relative-link correction.** The previous revision of this entry linked GLO12 and RTOFS as `../../france/glo12.md` and `../../us/rtofs-global.md`, which resolve to `models/ocean_models/france/` and `models/ocean_models/us/` — neither exists. Corrected to `../france/` and `../us/`. `riops.md` has the reciprocal problem: it links GIOPS as `./giops.md` from `models/ocean_models/regional/canada/`. **Worth a repository-wide sweep.**

---

## Official documentation
- GIOPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_giops/readme_giops_en/
- Datamart access and file nomenclature: https://eccc-msc.github.io/open-data/msc-data/nwp_giops/readme_giops-datamart_en/
- Variable list (CSV, JS-loaded on the page above): https://eccc-msc.github.io/open-data/assets/csv/GIOPS_Variables-List_en.csv
- Technical specifications, analysis component (serves v3.5.0): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GIOPS_e.pdf
- Technical specifications, GDPS — authoritative for the forecast component: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GDPS_e.pdf
- Technical note: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_giops_e.pdf
- v3.5.0 factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_giops_e.pdf
- GIOPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_giops/changelog_giops_en/
- GDPS changelog (tracks the forecast component): https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/changelog_gdps_en/
- ECCC multi-system changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- System dependency diagram: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_GIOPS_en.svg
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/
- Discovery metadata: https://open.canada.ca/data/en/dataset/dc3a7022-95e8-45a7-bf63-3d45b6cda0dc

### Key references
- Smith, G.C., Roy, F., Reszka, M., Surcel Colan, D., He, Z., Deacu, D., Belanger, J.M., Skachko, S., Liu, Y., Dupont, F., Lemieux, J.-F. (2016). Sea ice forecast verification in the Canadian global ice ocean prediction system. *Q. J. Roy. Meteor. Soc.*, 142(695), 659–671. https://doi.org/10.1002/qj.2555
- Smith, G.C., Bélanger, J.M., Roy, F., Pellerin, P., Ritchie, H., Onu, K., Roch, M., Zadra, A., Surcel Colan, D., Winter, B., Fontecilla, J.S. (2018). Impact of coupling with an ice-ocean model on global medium-range NWP forecast skill. *Mon. Wea. Rev.*, 146, 1157–1180. https://doi.org/10.1175/MWR-D-17-0157.1
- Smith, G.C., Liu, Y., Benkiran, M., Chikhar, K., Surcel Colan, D., Gauthier, A.A., Testut, C.E., Dupont, F., Lei, J., Roy, F., Lemieux, J.-F. (2021). The Regional Ice Ocean Prediction System v2: a pan-Canadian ocean analysis system using an online tidal harmonic analysis. *Geosci. Model Dev.*, 14(3), 1445–1467. https://doi.org/10.5194/gmd-14-1445-2021
- Husain, S.Z., et al. (2024). Leveraging data-driven weather models for improving numerical weather prediction skill through large-scale spectral nudging. *arXiv*:2407.06100. https://doi.org/10.48550/arXiv.2407.06100
- Pham, D.T., Verron, J., Roubaud, M.C. (1998). A singular evolutive extended Kalman filter for data assimilation in oceanography. *J. Mar. Syst.*, 16, 323–340.
- Lellouche, J.M., et al. (2013). Evaluation of global monitoring and forecasting systems at Mercator Océan. *Ocean Science*, 9(1), 57.
- Madec, G. and the NEMO team (2008). NEMO ocean engine. *Note du Pôle de modélisation*, Institut Pierre-Simon Laplace, No. 27.
- Hunke, E.C., Allard, R., et al. (2021). CICE-Consortium/CICE: CICE Version 6.2.0. Zenodo. https://doi.org/10.5281/zenodo.4671172
- Hunke, E.C. (2001). Viscous-plastic sea ice dynamics with the EVP model: linearization issues. *J. Comput. Phys.*, 170, 18–38.
- Lipscomb, W.H., Hunke, E.C., Maslowski, W., Jakacki, J. (2007). Ridging, strength, and stability in high-resolution sea ice models. *J. Geophys. Res.*, 112, C03S91. https://doi.org/10.1029/2005JC003355
- Briegleb, B.P., Light, B. (2007). A Delta-Eddington multiple scattering parameterization for solar radiation in the sea ice component of the Community Climate System Model. NCAR Technical Note NCAR/TN-472+STR.
- Pringle, D.J., Eicken, H., Trodahl, H.J., Backstrom, L.G.E. (2007). Thermal conductivity of landfast Antarctic and Arctic sea ice. *J. Geophys. Res. Oceans*. https://doi.org/10.1029/2006JC003641
- Gaspar, P., Gregoris, Y., Lefevre, J.M. (1990). A simple eddy kinetic energy model for simulations of the oceanic vertical mixing. *J. Geophys. Res.*, 95, 16179–16193.
- Blanke, B., Delecluse, P. (1993). Variability of the tropical Atlantic Ocean simulated by a general circulation model with two different mixed-layer physics. *J. Phys. Oceanogr.*, 23, 1363–1388.
