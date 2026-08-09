# RIOPS (Regional Ice-Ocean Prediction System)

## What this model is
The **Regional Ice-Ocean Prediction System (RIOPS)** is the operational regional ocean and sea ice forecast system of **Environment and Climate Change Canada (ECCC)**, run at the **Canadian Centre for Meteorological and Environmental Prediction (CCMEP)**. It produces 84-hour forecasts four times daily on a subset of the 1/12° ORCA12 tripolar grid covering the three Canadian oceans — part of the North Atlantic, the full Arctic, and part of the North Pacific — with 75 vertical levels and an online sea ice component.

RIOPS sits between [GIOPS](../../global/canada/giops.md) (global, 1/4°) and [CIOPS](./ciops.md) (coastal, 1/36°) in ECCC's three-tier ocean prediction stack. It is initialized from GIOPS analyses, runs its own SAM2 ocean assimilation, and supplies lateral boundary conditions and the nudging target for CIOPS.

Two features distinguish it. First, its **integrated ice analysis component RIPS** assimilates SAR imagery from Canada's **RADARSAT Constellation Mission** alongside Canadian Ice Service ice charts, passive microwave, and scatterometer data — an operational use of spaceborne SAR for sea ice that few other national services match. Second, it carries **online tidal harmonic analysis** (Smith et al., 2021), so the distributed currents include the tidal signal rather than a detided residual.

Unlike GIOPS, RIOPS has **a single, clean version line**: files stamp `Regional Ice Ocean Prediction System: version 2.5.0`, matching the documented operational version exactly. The current version is **v2.5.0**, implemented 14 April 2026 as part of ECCC's HPC migration; it is computationally identical to **v2.4.0** (11 June 2024), which brought CICE6, Delta-Eddington radiation, GDPS-G0 forcing, a new Mean Dynamic Topography, and RCM-SAR ice assimilation.

---

## Who runs it
- **Production Unit:** Canadian Centre for Meteorological and Environmental Prediction (CCMEP), Meteorological Service of Canada (MSC)
- **Country:** Canada
- **Programme:** MSC Open Data
- **Operational since:** 3 July 2019 (v2.0.0, when RIOPS moved from experimental to operational)
- **Role in any larger system:** parent system for [CIOPS](./ciops.md)-East and -West, which take RIOPS forecasts as lateral boundary conditions and spectrally nudge to RIOPS in the deep ocean. RIOPS does not feed back into [GIOPS](../../global/canada/giops.md) or [GDPS](../../../nwp_models/global/canada/gem-global.md).
- **Contact stamped in files:** `production-info@ec.gc.ca`

---

## What area it covers
- **Coverage:** Pan-Canadian, three oceans — part of the North Atlantic, the Arctic Ocean including the Canadian Arctic Archipelago, and part of the North Pacific
- **Model domain (per technical specification):** 25.6°N in the North Atlantic to 43.8°N in the Pacific
- **Native grid:** subset of the global ORCA12 tripolar grid, **1580 × 2198**, nominally 1/12° — approximately 8 km in the North Atlantic narrowing to 2 km in the Canadian Arctic Archipelago through meridional convergence
- **Vertical levels:** 75 z-levels

### Distributed grid (`PS5km`) — one grid only
- **Verified dimensions:** 1770 × 1610 at exactly 5000 m spacing; `xc` 0 → 8,845,000 m, `yc` 0 → 8,045,000 m
- **Projection parameters:** `latitude_of_projection_origin = 90.0`, `straight_vertical_longitude_from_pole = −100.0`, `scale_factor_at_projection_origin = 0.9330124` (true scale at 60°N), `false_easting = 4245000.0`, `false_northing = 5295000.0`, `earth_radius = 6371229.0`
- **Valid (in-domain) fraction:** 52.42% of grid points — 1,493,911 of 2,849,700
- **Verified domain edges in the distribution:** Pacific sector reaches **43.79°N**, matching the specification's 43.8°N. Atlantic sector reaches **28.92°N**, which is the grid's own minimum latitude.

> **The distributed grid is bit-identical to the GIOPS polar stereographic grid.** `xc`, `yc`, the 2D `latitude` and `longitude` auxiliary arrays, and every grid-mapping attribute are equal element-for-element between RIOPS `PS5km` files and GIOPS `ps5km60N` files (verified by array comparison). RIOPS and GIOPS fields can therefore be stacked, differenced, or composited **without any regridding** — a genuinely useful property that neither product's documentation mentions, and one obscured by the two different grid tokens in the filenames.

> **The North Atlantic domain is truncated by the distribution grid.** The specification puts the model's southern Atlantic boundary at 25.6°N, but valid data reaches the grid's bottom row (473 valid points at 28.92°N, longitudes 282.9°–301.0°E) and stops there. Valid data touches all four grid edges. Roughly three degrees of latitude of the modelled Atlantic domain is not distributed. The Pacific boundary, by contrast, is set by the model rather than the grid.

### Masked regions
Land, below-seafloor, and out-of-domain points use `_FillValue = 1e+20`. The ice mask and the ocean mask are the same domain mask, differing by exactly one grid point. **Sea ice fields are zero-filled over open water, not masked** — see *Notes*.

---

## Basic details
- **Model type:** Regional deterministic ocean physics and sea ice, with its own data assimilation and an integrated ice analysis
- **Core ocean model:** NEMO 3.6 / OPA (Madec et al., 1998; Madec and NEMO team, 2008)
- **Sea ice model:** CICE 6.2.0 (Hunke et al., 2021), upgraded from CICE 4.0 in v2.4.0
- **System name:** RIOPS v2.5.0
- **Horizontal resolution:** 1/12° nominal (~8 km North Atlantic to ~2 km Canadian Arctic Archipelago); distributed at 5 km polar stereographic
- **Vertical coordinate:** z-level, `positive = down`
- **Verified depth levels (75):** 0.50753, 1.5578, 2.6693, 3.8583, 5.1417, 6.5447, 8.0939, 9.8239, 11.7746, 13.9917, 16.5271, 19.4308, 22.7594, 26.5586, 30.8755, 35.7407, 41.1806, 47.2129, 53.8515, 61.1136, 69.0227, 77.6127, 86.9312, 97.0426, 108.032, 120.001, 133.077, 147.408, 163.166, 180.552, 199.792, 221.143, 244.892, 271.358, 300.889, 333.865, 370.690, 411.796, 457.628, 508.642, 565.294, 628.028, 697.261, 773.370, 856.681, 947.450, 1045.86, 1151.99, 1265.86, 1387.38, 1516.37, 1652.57, 1795.67, 1945.30, 2101.03, 2262.42, 2429.03, 2600.38, 2776.04, 2955.57, 3138.57, 3324.64, 3513.45, 3704.66, 3897.98, 4093.16, 4289.96, 4488.16, 4687.58, 4888.07, 5089.48, 5291.69, 5494.58, 5698.06, **5902.06**
- **Forecast length:** 84 h
- **Update frequency:** 4× daily
- **Production cycles:** 00, 06, 12, 18 UTC. The forecast job starts at T+4 (4-hour observation cutoff).
- **Temporal output resolution (verified):** **hourly, 85 steps (000–084), instantaneous** — every distributed variable carries `cell_methods = time: point`. Step `P000` is the instantaneous state at cycle time.
- **Numerical technique:** primitive equations, finite differences, Arakawa C-grid; explicit leapfrog with a non-linear free surface solved explicitly (time-splitting of barotropic and baroclinic stepping); **baroclinic time step 300 s**
- **Horizontal diffusion:** bi-Laplacian on momentum along geopotential coordinates; Laplacian on tracers along iso-neutral surfaces
- **Vertical mixing:** k-ε turbulent mixing scheme (Umlauf and Burchard, 2003), with reduced TKE injection by wave breaking (Rascle et al., 2008)
- **Surface scheme:** GEM bulk formulae for turbulent sensible heat, latent heat, and momentum
- **Bathymetry source:** **Etopo1**, with smoothing applied to accommodate high tides
- **Archive availability:** rolling 30 days on Datamart

---

## Forcing
- **Atmospheric forcing:** winds, surface pressure, radiative fluxes and humidity from **GDPS-G0 v9.0.0** at ~10 km, one-way. Replaced RDPS forcing in v2.4.0. *GDPS has since moved to 9.1.0 and then 10.0.0, so the published `v9.0.0` designation is stale — see* Notes.
- **Lateral oceanic boundary conditions:** oceanic OBC from **GDPS-G1 v9.0.0**
- **River runoff:** real-time St. Lawrence River discharge from the **CanHys** database, switched from the IML feed in v2.4.0. The St. Lawrence freshwater flux materially controls salinity, stratification and circulation across the Gulf of St. Lawrence and the Scotian Shelf.
- **Tidal forcing:** **included**, via online tidal harmonic analysis (Smith et al., 2021). Distributed currents and sea surface height therefore contain the tidal signal — this is not a detided product. Contrast [GIOPS](../../global/canada/giops.md), which has no tides at all.
- **Initial conditions:**
  - **00 UTC:** from the RIOPS assimilation analysis
  - **06, 12, 18 UTC:** from the previous cycle's forecast restart dump at lead time 6 h
  - **Sea ice:** from the RIPS_early regional ice analysis (4-hour cutoff, RCM-SAR assimilated), at every cycle

---

## Coupling

### Sea ice (CICE 6.2.0) — online
- **Radiation:** Delta-Eddington (Briegleb and Light, 2007), with **`R_ice = 0.0`, `R_pnd = 0.0`, `R_snw = 1.0`**
- **Conductivity:** "bubbly" scheme (Pringle et al., 2007)
- **Ice strength:** P\* = 22.5 kN/m², C\* = 15
- **Ice-ocean roughness:** `Z0io` / `iceruf_ocn` = 1.8 cm
- **Air-ice roughness:** `iceruf` = 0.54 mm

> **The Delta-Eddington tuning differs from GIOPS.** GIOPS 3.5.0 uses `R_ice = 2.0; R_pnd = 2.0; R_snw = 2.0`; RIOPS 2.4.0 uses `0.0; 0.0; 1.0`. Same scheme, same CICE version, deliberately different tuning constants — so the two systems' ice albedo and shortwave penetration behaviour are not interchangeable, even where their grids are identical.

### Atmosphere — one-way
RIOPS receives GDPS-G0 fluxes but does not feed back into the atmosphere within the forecast cycle. This is the sharpest architectural difference from [GIOPS](../../global/canada/giops.md), whose forecast component is two-way coupled inside GDPS.

### Waves
No wave coupling.

---

## Data assimilation

RIOPS runs **two independent assimilation systems**: an ocean analysis (SAM2) and an ice analysis (RIPS).

### Ocean analysis
- **DA scheme:** SAM2 (Système d'Assimilation Mercator), reduced-order Kalman filter with SEEK formulation (Pham et al., 1998) — same algorithm family as [GIOPS](../../global/canada/giops.md) and [GLO12](../../global/france/glo12.md)
- **Analysed variables:** T, S, U, V, SSH, ice concentration
- **Update cycle:** two weekly analyses (delayed and real-time) valid on Wednesday, plus a daily analysis update at 00 UTC
- **Increment application:** IAU over the analysis period — **7 days for the weekly cycle, 1 day for the daily cycle**
- **Analysis increment grid:** coarsened, one point every 4 in each direction
- **Trial fields:** 168-hour forecasts (RR and RD streams) and 24-hour forecast (RU stream)
- **Background-error covariances:** modelled 3D anomalies from a multi-year hindcast simulation
- **Bias correction:** 3D-Var temperature and salinity bias correction scheme (added v2.2.0)
- **In-situ quality control:** DFO-QC, inherited from the GIOPS assimilation (v2.3.1)

**Assimilated ocean observations:** CCMEP gridded SST analysis at 0.1° (itself ingesting AVHRR from MetOp-A/B/C, VIIRS from Suomi NPP and NOAA-20, AMSR2 from GCOM-W1, and in-situ buoy/drifter/ship data); sea level anomaly from six altimeters — SARAL/AltiKa, CryoSat-2N, Jason-3N, Sentinel-3A, Sentinel-3B, Sentinel-6A-HR; and temperature/salinity profiles from Argo, drifters, bathythermographs, gliders, ships of opportunity, buoys and moorings.

### Ice analysis (RIPS v2.4.0)
- **Algorithm:** **2D-Var** built on ECCC's MIDAS assimilation infrastructure
- **Analysed variables:** ice concentration and ice concentration analysis error
- **Domain and resolution:** ORCA12 grid corrected around the North Pole, 0.045° (~5 km)
- **Assimilation window:** 6 hours, centred on analysis time
- **Observation QC:** uses 10 m winds and 2 m temperature to screen observations
- **Assimilated ice observations:** SAR from the three-satellite RADARSAT Constellation Mission (Komarov et al., 2020); Canadian Ice Service daily ice charts, image analysis charts and lake bulletins; SSM/IS from DMSP-17 and DMSP-18; ASCAT from MetOp-B and MetOp-C; AMSR2
- **Cycles:** `RIPS_late` — 4 per day (00, 06, 12, 18 UTC), 7-hour cutoff. `RIPS_early` — 4 per day, 4-hour cutoff, using `RIPS_late` as background. **`RIPS_early` is what initializes the RIOPS forecast.**

The late/early split is a neat operational compromise: the 7-hour-cutoff analysis gets maximum data for the record, while the 4-hour-cutoff analysis keeps the forecast on schedule.

---

## What it provides

Seventeen 2D variables and four 3D variables — the same variable set as [GIOPS](../../global/canada/giops.md), on the same grid, at every one of the 85 hourly steps.

### 3D fields (`DBS-all`, 75 levels)
| Variable | `nomvar` | Units in file | Standard name |
|---|---|---|---|
| `votemper` | `TM` | `Kelvin` | `sea_water_potential_temperature` |
| `vosaline` | `SALW` | `1e-3` | `sea_water_salinity` |
| `vozocrtx` | `UUW` | `m s-1` | `sea_water_x_velocity` |
| `vomecrty` | `VVW` | `m s-1` | `sea_water_y_velocity` |

**Neither vertical velocity nor turbulent kinetic energy is distributed.** The specification lists TKE as a prognostic variable and vertical velocity as a derived one, but no public file contains either.

### Surface and near-surface fields (2D)
The four ocean variables above are also issued as 2D files at `DBS-0.5m` (first model level), plus:

| Variable | `nomvar` | Units | Notes |
|---|---|---|---|
| `sossheig` | `SSH` | `m` | `sea_surface_height_above_geoid` — **includes the tidal signal** |
| `sokaraml` | `MLW` | `m` | Mixed layer depth, density criterion |
| `somixhgt` | `MLTW` | `m` | Turbocline depth — a distinct diagnostic, not a duplicate |

### Sea ice fields (2D)
| Variable | `nomvar` | Units | Description |
|---|---|---|---|
| `iiceconc` | `GL` | `1` | Sea ice area fraction |
| `iicevol` | `GE` | `m` | Sea ice **volume per unit grid cell area** — not ice thickness |
| `isnowvol` | `SDV` | `cm` | Snow volume per unit grid cell area, in centimetres |
| `itzocrtx` | `UUI` | `m s-1` | Sea ice x velocity |
| `itmecrty` | `VVI` | `m s-1` | Sea ice y velocity |
| `iicesurftemp` | `TMI` | `Kelvin` | Surface temperature of snow over sea ice, or bare ice |
| `iicestrength` | `STGI` | `N m-1` | Depth-integrated compressive ice strength |
| `iicepressure` | `SIII` | `N m-1` | Depth-integrated internal ice pressure |
| `iicedivergence` | `DIVI` | `%/day` | Sea ice divergence |
| `iiceshear` | `SHRI` | `%/day` | Sea ice shear strain rate |

### Static fields
None distributed — no bathymetry, land-sea mask, or cell-area files. The domain mask must be inferred from `_FillValue`.

---

## Data availability

- **Is the data free?** Yes — no registration, no API key, direct HTTPS
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required. Suggested attribution: "Data Source: Environment and Climate Change Canada." https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded only
- **Data formats:** NetCDF-4 classic model (HDF5 container), `Conventions = CF-1.6`, **zlib level 1 with shuffle**. Chunking: 2D `[1, 805, 885]`, 3D `[1, 19, 403, 443]`. Time encoded as seconds since 1950-01-01 00:00:00, gregorian calendar.
- **Official download location:**
  - Current day: https://dd.weather.gc.ca/today/model_riops/netcdf/forecast/polar_stereographic/
  - Dated archive: `https://dd.weather.gc.ca/{YYYYMMDD}/WXO-DD/model_riops/netcdf/forecast/polar_stereographic/{nd}/{HH}/{hhh}/`
  - where `{nd}` is `2d` or `3d`, `{HH}` is `00`/`06`/`12`/`18`, and `{hhh}` runs `000`–`084` hourly
- **File naming:** `{YYYYMMDD}T{HH}Z_MSC_RIOPS_{VAR}_{LVL}_PS5km_P{hhh}.nc`
  - `{VAR}` uppercase (`IICECONC`, `VOTEMPER`, …); `{LVL}` one of `SFC`, `DBS-0.5m`, `DBS-all`
  - Example: `20260808T18Z_MSC_RIOPS_VOMECRTY_DBS-all_PS5km_P000.nc`
  - **This is the newer MSC naming scheme**, not the legacy `CMC_*` convention that [GIOPS](../../global/canada/giops.md) still uses.
- **Files per cycle (live-confirmed on all four 2026-08-08 cycles):** **1,785** — 1,445 2D (17 variables × 85 steps) and 340 3D (4 × 85)
- **Volume:** ~14.54 GiB 2D + ~40.0 GiB 3D = **~54.6 GiB per cycle, ~218 GiB and 7,140 files per day.** This is roughly **five times the daily volume of GIOPS**, driven by hourly rather than 24-hourly 3D output.
- **File size:** 2D 9.4–12 MiB. 3D: `VOTEMPER` ~76 MiB, `VOSALINE` ~84 MiB, `VOZOCRTX` 154–163 MiB, `VOMECRTY` 159–168 MiB. The velocity fields are roughly twice the size of the tracers because they are far less spatially smooth.
- **Retention:** dated directories persist **30 days** — live-probed 2026-08-09: `20260710` present, `20260709` returns 404 (same policy as GIOPS, GDSPS and RESPS)
- **Publication latency (three-day sample, 2026-08-05 to 2026-08-08):** each cycle is written as a single ~4-minute burst.
  - 06Z, 12Z, 18Z complete at roughly **T+5h00m to T+5h15m**
  - 00Z completes later, roughly **T+5h36m to T+5h52m** — consistently 30–50 minutes behind the other three, consistent with the 00Z cycle waiting on the daily assimilation analysis rather than a 6-hour restart
- **Push notification:** available via AMQP (MSC Datamart sr3/sarracenia)
- **Other access:** MSC GeoMet serves **163 RIOPS layers** as WMS/WCS (`RIOPS_IICECONC_SFC`, `RIOPS_VOSALINE_DBS-1046m`, …). A WMS view is also available at https://www.meteocentre.com/plus under the CMC experimental tab.
- **Discovery metadata:** https://open.canada.ca/data/en/dataset/66caa8cc-0e9c-4fdb-ae40-fab9c255b811

---

## Version history

### April 14, 2026 — RIOPS v2.5.0 (current)
- Migration to ECCC's new High Performance Computing infrastructure
- Computational only; no scientific change. **Confirmed by the file version stamp**, which reads `2.5.0` across the current archive.

### June 11, 2024 — RIOPS v2.4.0
- Sea ice model upgraded from CICE 4.0 to **CICE 6.2.0**
- Delta-Eddington radiation scheme introduced (`R_ice = 0.0; R_pnd = 0.0; R_snw = 1.0`)
- "Bubbly" thermal conductivity scheme
- **Atmospheric forcing switched from RDPS to GDPS-G0**; oceanic OBC from GDPS-G1
- **St. Lawrence freshwater flux switched from the IML feed to the CanHys database**
- New Mean Dynamic Topography reference
- Three new altimeters activated (CryoSat-2N, Jason-3N, Sentinel-6), roughly doubling assimilated altimetry
- **RCM-SAR data added to the RIPS ice analysis**; ORCA12 grid corrected around the North Pole

### November 28, 2023 — RIOPS v2.3.1
- No model or assimilation change. In-situ observations now supplied by the DFO-QC component introduced in GIOPS v3.4.1.

### June 28, 2022 — RIOPS v2.3.0
- HPC infrastructure migration (computational only)

### December 1, 2021 — RIOPS v2.2.0
- New vertical blending schema
- 3D-Var bias correction for temperature and salinity profiles
- Reduced TKE injection by wave breaking (Rascle et al., 2008)
- Increased ice roughness; new diurnal cycle handling; new monitoring package

### January 21, 2020 — RIOPS v2.1.0
- HPC infrastructure migration (computational only)

### July 3, 2019 — RIOPS v2.0.0 (declared operational)
- **Status changed from experimental to operational**
- **Independent oceanic data assimilation component added, replacing the previous spectral nudging to GIOPS**
- **Domain extended to the North Pacific**

### June 21, 2016 — RIOPS v1.1 (experimental)
- First implementation, superseding the earlier experimental Regional Ice Prediction System (RIPS)
- 3D ocean component (NEMO-CICE) with tides added
- Large-scale nudging to GIOPS analyses during the pseudo-analysis step, with forecast from 00 UTC
- AVHRR added to the ice concentration analysis

---

## Notes

- **Sea ice fields are zero-filled, not masked — the opposite of GIOPS, on an identical grid.** In RIOPS, `iiceconc` is valid across the entire ocean domain (52.42% of grid points) with a minimum of exactly `0.0`, and **71% of valid points are exactly zero**. In [GIOPS](../../global/canada/giops.md), every point below 1% concentration is `_FillValue`, leaving only 10.82% of the same grid valid. Anyone differencing RIOPS against GIOPS, or reusing GIOPS-derived masking code, will get wrong answers unless this is handled explicitly. The RIOPS ice mask and ocean mask are the same domain mask, differing by exactly one grid point — itself an oddity, though a harmless one.

- **The deepest depth coordinate is silently masked by CF-aware readers.** The `depth` variable declares `valid_max = 5875.0` m, but the 75th level is at **5902.06 m** — exceeding the declared maximum by 27.06 m. netCDF4-python with auto-masking on, xarray, and anything else honouring `valid_max` will return the bottom depth as masked, turning the depth coordinate into a masked array and breaking naive indexing. The data at level 75 is 100% fill within the RIOPS domain (and level 74 is 99.54% fill), so nothing numerical is lost — but the coordinate defect is real. Read with `set_auto_mask(False)` to recover the value.

- **The Datamart page states the wrong number of vertical levels, and the error propagates into the machine-readable variable list.** The RIOPS Datamart page's *Levels* section reads "The three-dimensional fields are provided on 50 depth levels" and then lists the **GIOPS** depth values verbatim (0.494025 … 5727.92). RIOPS has **75** levels running to 5902.06 m, as both the technical specification and the factsheet correctly state, and as the files confirm. The same "50 depth levels" string appears in `RIOPS_Variables-List_en.csv`. This is a copy-paste of the GIOPS documentation, and it is the single most consequential documentation error in this product.

- **Two more copy-paste errors on the same page.** It states that visiting the data directory yields "a raw listing of links, each link being a downloadable **GRIB2** file" — the files are NetCDF throughout. And it describes the assimilation as "a **3DVar** ice concentration analysis" while "the large scales of the ocean analysis are constrained by **spectrally nudging** the temperature and salinity fields to those of GIOPS." Both are stale: the ice analysis is **2D-Var on MIDAS**, and spectral nudging was **replaced by independent ocean assimilation in v2.0.0 in July 2019**. The page describes a system that has not existed for seven years.

- **The `forecast/` path segment implies an analysis tree that does not exist.** The Datamart layout is `netcdf/forecast/polar_stereographic/`, and the readme says RIOPS "produces regional sea ice and ocean analyses and 84 hours forecasts." Only the forecast is distributed — `netcdf/analysis/` returns nothing, and there is no `lat_lon/` sibling either. Neither the ocean analysis nor the RIPS ice analysis is public, despite RIPS being the component with the most operationally distinctive input (RCM-SAR).

- **Output is instantaneous, and here the `title` attribute is correct.** Every variable carries `cell_methods = time: point`, and the files are titled `Instantaneous sea ice and ocean forecast fields` (2D) or `Instantaneous ice and ocean forecast fields` (3D). The same two title strings appear in GIOPS files, where they are **wrong** — GIOPS forecast output is 3-hourly and 24-hourly means. The strings are shared boilerplate that happens to describe RIOPS accurately and GIOPS inaccurately; do not treat the title as a reliable discriminator in either product.

- **Currents and sea surface height include tides.** RIOPS runs online tidal harmonic analysis, so `vozocrtx`, `vomecrty` and `sossheig` contain the tidal signal. Users wanting subtidal circulation must filter it out themselves. This also means hourly output is not merely convenient but necessary — 3-hourly sampling would alias the semidiurnal constituents.

- **Temperatures are in Kelvin, and file units disagree with ECCC's variable table.** As with GIOPS, the published CSV gives `K`, `PSU` and `Fraction` where the files carry `Kelvin`, `1e-3` and `1`. The file values are CF-conformant; the table is looser.

- **The published GDPS forcing version is stale, and the AI question is unresolved.** The v2.4.0 specification names GDPS-G0 and GDPS-G1 at **v9.0.0**. GDPS moved to 9.1.0 in April 2026 and to **10.0.0 on 26 May 2026**, the release that made the operational atmosphere a hybrid physics-AI system spectrally nudged toward [GEML](../../../nwp_models/global/canada/gdps-geml.md). Whether the G0 and G1 components supplying RIOPS forcing and boundary conditions carry that nudging is **not documented anywhere, and unlike GIOPS the RIOPS files give no clue** — they stamp only the RIOPS version. **TBD:** confirm with CCMEP before asserting that RIOPS is AI-influenced. If it is, the same reasoning extends downstream to [CIOPS](./ciops.md). See the corresponding flag in the [GIOPS entry](../../global/canada/giops.md) regarding whether these systems belong in [`AI_MODELS.md`](../../../../AI_MODELS.md).

- **Volume is the practical constraint.** At ~218 GiB/day across 7,140 files, RIOPS is among the largest freely distributed operational ocean products in this catalog, and a full 30-day mirror would run to roughly 6.4 TiB. The 3D velocity fields alone account for over half of it. Users who need only surface fields should take the `2d/` tree, which is under 60 GiB/day for all four cycles combined.

- **Relative-link correction.** The previous revision linked GIOPS as `./giops.md` and GLO12 as `../../france/glo12.md` from `models/ocean_models/regional/canada/`; neither path resolves. Corrected to `../../global/canada/giops.md` and `../../global/france/glo12.md`. The same class of error exists elsewhere in the repository — **a repository-wide relative-link sweep is still outstanding.**

---

## Official documentation
- RIOPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_riops/readme_riops_en/
- Datamart access and file nomenclature: https://eccc-msc.github.io/open-data/msc-data/nwp_riops/readme_riops-datamart_en/
- Variable list (CSV, JS-loaded on the page above): https://eccc-msc.github.io/open-data/assets/csv/RIOPS_Variables-List_en.csv
- Technical specifications (serves v2.4.0): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RIOPS_e.pdf
- Technical note: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_notes/technote_riops_e.pdf
- Factsheet (v2.4.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_riops_e.pdf
- RIOPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_riops/changelog_riops_en/
- ECCC multi-system changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- System dependency diagram: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_RIOPS_en.svg
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/
- Discovery metadata: https://open.canada.ca/data/en/dataset/66caa8cc-0e9c-4fdb-ae40-fab9c255b811

### Key references
- Smith, G.C., Liu, Y., Benkiran, M., Chikhar, K., Surcel Colan, D., Gauthier, A.A., Testut, C.E., Dupont, F., Lei, J., Roy, F., Lemieux, J.-F. (2021). The Regional Ice Ocean Prediction System v2: a pan-Canadian ocean analysis system using an online tidal harmonic analysis. *Geosci. Model Dev.*, 14(3), 1445–1467. https://doi.org/10.5194/gmd-14-1445-2021
- Komarov, A.S., Caya, A., Buehner, M., Pogson, L. (2020). Assimilation of SAR ice and open water retrievals in Environment and Climate Change Canada Regional Ice-Ocean Prediction System. *IEEE Trans. Geosci. Remote Sens.*, 58(6), 4290–4303. https://doi.org/10.1109/TGRS.2019.2962656
- Pham, D.T., Verron, J., Roubaud, M.C. (1998). A singular evolutive extended Kalman filter for data assimilation in oceanography. *J. Mar. Syst.*, 16, 323–340.
- Madec, G. and the NEMO team (2008). NEMO ocean engine. *Note du Pôle de modélisation*, Institut Pierre-Simon Laplace, No. 27.
- Madec, G., Delecluse, P., Imbard, M., Lévy, C. (1998). OPA 8.1 Ocean General Circulation Model reference manual. *Note du Pôle de modélisation*, Institut Pierre-Simon Laplace.
- Hunke, E.C., Allard, R., et al. (2021). CICE-Consortium/CICE: CICE Version 6.2.0. Zenodo. https://doi.org/10.5281/zenodo.4671172
- Hunke, E.C. (2001). Viscous-plastic sea ice dynamics with the EVP model: linearization issues. *J. Comput. Phys.*, 170, 18–38.
- Lipscomb, W.H., Hunke, E.C., Maslowski, W., Jakacki, J. (2007). Ridging, strength, and stability in high-resolution sea ice models. *J. Geophys. Res.*, 112, C03S91. https://doi.org/10.1029/2005JC003355
- Briegleb, B.P., Light, B. (2007). A Delta-Eddington multiple scattering parameterization for solar radiation in the sea ice component of the Community Climate System Model. NCAR Technical Note NCAR/TN-472+STR.
- Pringle, D.J., Eicken, H., Trodahl, H.J., Backstrom, L.G.E. (2007). Thermal conductivity of landfast Antarctic and Arctic sea ice. *J. Geophys. Res. Oceans*. https://doi.org/10.1029/2006JC003641
- Umlauf, L., Burchard, H. (2003). A generic length-scale equation for geophysical turbulence models. *J. Mar. Res.*, 61, 235–265.
- Rascle, N., Ardhuin, F., Queffeulou, P., Croizé-Fillon, D. (2008). A global wave parameter database for geophysical applications. Part 1. *Ocean Modelling*, 25, 154–171. https://doi.org/10.1016/j.ocemod.2008.07.006
