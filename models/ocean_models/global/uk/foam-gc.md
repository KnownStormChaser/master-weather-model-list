# Met Office Global Ocean (FOAM Global Coupled — FOAM-GC)

## What this model is
The Met Office Global Ocean product is the ocean–sea-ice output of the **Met Office Global Coupled Atmosphere-Land-Ocean-Ice system**, operational since May 2022. The ocean–ice component is the **Forecast Ocean Assimilation Model, Global Coupled configuration (FOAM-GC)**: a global physical ocean and sea-ice analysis and 7-day forecast, interpolated from the native tripolar model grid to a regular latitude–longitude grid by the Met Office Global Marine Post-Processing (MaPP-GL) system.

Unlike a standalone forced-ocean system, FOAM-GC runs *inside* a fully coupled model: the atmosphere-land component (Unified Model / JULES) and the ocean-ice component exchange fluxes every hour via the OASIS3-MCT coupler, with weakly-coupled data assimilation. The published products are the "level-1" ocean and ice fields — 3D daily-mean temperature/salinity/currents, 2D daily-mean surface and ice fields, and instantaneous hourly surface fields.

Only the **deterministic** system's ocean/ice products are published (the coupled system also runs a 44-member atmospheric ensemble, which is not distributed here).

---

## Who runs it
- **Production Unit:** UK Met Office
- **Country:** United Kingdom
- **Programme or coordinating body:** Met Office operational suite (Global Coupled Atmosphere-Land-Ocean-Ice system); ocean/ice component is FOAM-GC
- **Role in any larger system:** Ocean–ice component of the Met Office global coupled NWP system; provides the interactive ocean beneath the deterministic and ensemble atmospheric forecasts. The global ocean state is also the parent for regional shelf downscalings (e.g. AMM15/NWS — parent configuration worth confirming; see cross-references).

---

## What area it covers
- **Coverage:** Global
- **Domain bounds:** 0–359.75°E, 83°S–89.75°N (regular lat-lon delivery grid)
- **Grid dimensions:** 1440 × 692 (verified from live files, 2026-07-24)
- **Special masked or excluded regions:** Land masked; sea-ice fields populated only where ice is present. Native model uses the ORCA025 tripolar grid; northern fold handled natively before interpolation to the regular grid.

---

## Basic details
- **Model type:** Global ocean physics + sea ice; deterministic; coupled (ocean-ice half of a coupled atmosphere-land-ocean-ice system)
- **Core ocean model:** NEMO — GO6 Global Ocean configuration (Storkey et al., 2018; NEMO 3.6-based), from Global Coupled model version 4 (GC4)
- **Sea ice model:** CICE — GSI8.1 Global Sea Ice configuration (Ridley et al., 2018)
- **System name:** FOAM Global Coupled (FOAM-GC); coupled system components GA8 (atmosphere) / GL9 (land) / GO6 (ocean) / GSI8.1 (ice)
- **Horizontal resolution:** Native ORCA025 tripolar ¼° (~25 km); **delivered** on a regular ¼° (0.25°) lat-lon grid
- **Vertical levels:** Native model 75 levels (1 m near surface to 200 m at depth); **delivered on 43 levels, 0–5500 m** (0, 5, 10, 15, 20, 25, 30, 40, 50, 60, 75, 100, 125, 150, 175, 200, 225, 250, 300, 400, 500, 600, 700, 800, 900, 1000, 1100, 1200, 1300, 1400, 1500, 1750, 2000, 2250, 2500, 2750, 3000, 3250, 3500, 4000, 4500, 5000, 5500 m)
- **Vertical coordinate:** Z-level
- **Forecast length:** 7 days (plus 2 days of analysis/best-estimate: day-2 best estimate, day-1 analysis)
- **Update frequency:** Once daily (ocean/ice products generated from the 00Z 7-day forecast; the coupled system itself cycles 4×/day at 00/06/12/18Z, but only the 00Z ocean products are published)
- **Production cycles:** Products from the 00Z cycle only (single `T0000Z` folder per day on AWS)
- **Target delivery time:** 12:00 UTC (usually available by ~10:30 UTC)
- **Temporal output resolution:** Daily mean (3D T/S/U/V, 2D surface + ice); hourly instantaneous (surface SSH, SST, surface currents)
- **Archive availability:** 2-year rolling archive on AWS Open Data
- **Bathymetry source:** GO6 standard bathymetry (TBD — specific source, likely GEBCO-derived; not stated in the PUM)

---

## Forcing
- **Atmospheric forcing:** Not one-way forced — the ocean is **hourly-coupled** to the Unified Model atmosphere (GA8) and JULES land (GL9) via OASIS3-MCT
- **River runoff:** GO6 standard runoff climatology (TBD — exact dataset not stated in PUM)
- **Lateral boundary conditions:** N/A (global)
- **Tidal forcing:** None explicit in the global ocean configuration
- **Ice forcing or coupling:** Interactive CICE sea ice, coupled to the ocean and atmosphere
- **Initial conditions:** Weakly-coupled data assimilation (NEMOVAR incremental 3D-Var FGAT for ocean and sea ice; separate atmosphere/land analyses); increments added back into the coupled model. Analysis-only update models (GLU/GLV/GLW) run at increasing latency (up to 15h15m) to assimilate late-arriving marine observations and constrain subsequent cycles.

Assimilated marine observations include satellite SST (MetOp-B AVHRR, NOAA-20/Suomi-NPP VIIRS, Sentinel-3A/B SLSTR, GCOM-W1 AMSR2 via GHRSST), in-situ SST (moored/drifting buoys, ships), satellite sea-level anomaly (CryoSat-2, SARAL/AltiKa, Jason-3, Sentinel-3A/B, Sentinel-6A via CMEMS SEALEVEL_GLO_PHY_L3_NRT_008_044, using CNES CLS13 MDT), sub-surface T/S profiles (Argo, gliders, moored buoys, marine mammals, manual), and satellite sea-ice concentration (SSMIS via EUMETSAT OSI-SAF).

---

## Coupling
- **Coupled system:** Ocean-ice half of the Met Office Global Coupled Atmosphere-Land-Ocean-Ice system. Atmosphere-land and ocean-ice components exchange fluxes **every hour** via OASIS3-MCT. This is a genuine two-way coupled forecast, not an ocean run under prescribed atmospheric fluxes — a key distinction from the peer standalone global ocean systems (GLO12, RTOFS).

---

## What it provides
Daily-mean 3D fields, daily-mean 2D surface/ice fields, and hourly instantaneous 2D surface fields. Variable names (NetCDF), verified from live files:

- `thetao` — sea water potential temperature [°C] (3D daily mean; 2D surface hourly)
- `bottomT` — bottom potential temperature [°C] (2D daily mean)
- `so` — sea water salinity [PSU] (3D daily mean)
- `uo`, `vo` — zonal / meridional velocity [m s⁻¹] (3D daily mean; 2D surface hourly)
- `zos` — sea surface height above geoid [m] (2D daily mean; 2D hourly)
- `mlotst` — ocean mixed layer thickness [m] (2D daily mean)
- `siconc` — sea ice area fraction (2D daily mean)
- `sithick` — sea ice thickness [m] (2D daily mean)
- `usi`, `vsi` — eastward / northward sea ice velocity [m s⁻¹] (2D daily mean)

### Static fields
- Bathymetry, land-sea mask, grid coordinates (embedded as CF coordinate/`crs` variables in each file; no separate static file observed on AWS)

---

## Data availability
- **Is the data free?** Yes — anonymous S3 access, no registration
- **License:** **CC BY-SA 4.0** (British Crown copyright 2024–2025, Met Office). Note the **share-alike** obligation: derivative/adapted datasets must be distributed under the same licence, in addition to attribution. Stricter than the plain CC-BY commonly used for Copernicus Marine products.
- **Is the data downloadable?** Yes — direct HTTPS/S3
- **Data formats:** NetCDF-4 (lossless compression); **CF-1.8 and ACDD-1.3 conventions** (live files report `CF-1.8,ACDD-1.3`; note the PUM text still says CF-1.7)
- **Product identifier:** Met Office "level-1" Global Ocean products (`level1_coupled_orca025_GL4`); AWS registry: *Met Office Global Ocean model on a 2-year rolling archive*
- **Dataset identifiers (per-parameter files):** `BED` (bottom T), `CUR` (currents), `ICE` (sea-ice variables), `MLD` (mixed-layer depth), `SAL` (salinity), `SSH` (sea surface height), `TEM` (potential temperature). Frequencies: `dm` (daily mean), `hi` (hourly instantaneous — CUR, SSH, TEM only). Each parameter/frequency has 9 validity-day files per bulletin (2 analysis + 7 forecast).
- **File naming:** `level1_coupled_orca025_GL4_{PARAM}_b{YYYYMMDD}_{freq}{YYYYMMDD}.nc`, where the first date is the bulletin date and the second is the validity date; `freq` ∈ {`dm`, `hi`}. Bucket path: `global-ocean-ORCA025/{YYYY}/{MM}/{DD}/T0000Z/`. A per-day JSON manifest (`{...}_GL4_shub.json`) at the day root lists all files.
- **File size:** ~230 KB (BED) up to ~57 MB (CUR daily-mean 3D) per file; SAL ~28 MB; hourly CUR ~45 MB
- **Official access:**
  - AWS registry: https://registry.opendata.aws/met-office-global-ocean/
  - Bucket: `s3://met-office-global-ocean-model-data/` (region `eu-west-2`)
  - Browse: https://met-office-global-ocean-model-data.s3.eu-west-2.amazonaws.com/index.html
  - CLI: `aws s3 ls --no-sign-request s3://met-office-global-ocean-model-data/`
  - Met Office data channels: https://www.metoffice.gov.uk/services/data/external-data-channels
- **DOI:** TBD (none published on the registry)
- **Delivery mechanism:** AWS Open Data (S3, anonymous). New-object notifications via SNS topic `arn:aws:sns:eu-west-2:633885181284:met-office-global-ocean-model-data-object_created`.

---

## Version history

### May 2022 — Operational implementation
- Global Coupled Atmosphere-Land-Ocean-Ice system operational at the Met Office; ocean/ice products generated via MaPP-GL from the deterministic system.
- Ocean-ice configuration: NEMO GO6 + CICE GSI8.1, ORCA025 ¼° / 75 levels; NEMOVAR 3D-Var FGAT assimilation.
- Coupled model components at GC4 (GA8/GL9/GO6/GSI8.1); details in Xavier et al. (2024).

*(Earlier FOAM lineage — including the standalone (non-coupled) global FOAM at ¼° and later ¹⁄₁₂° — predates this coupled product; document separately if a standalone FOAM entry is added. See Barbosa Aguiar et al., 2024.)*

---

## Relationship to other ocean products

### Companion products from same operator
- **[UK Met Office Global Wave Model (GloWave)](../../../wave_models/global/uk/ukmo-wave.md)** — global wave companion, same Unified Model atmospheric forcing lineage.
- **[Met Office NWS Ocean / AMM15 (FOAM-NWSO)](../../regional/uk/amm15-nws.md)** — the regional North-West European Shelf physics downscaling; takes lateral boundary conditions from a Met Office global FOAM ocean (exact parent configuration relative to FOAM-GC worth confirming).
- Atmospheric siblings in the same coupled system: **[Met Office Global (deterministic)](../../../nwp_models/global/uk/ukmo-global.md)** and **[MOGREPS-G (ensemble)](../../../ensemble_models/global/uk/mogreps-g.md)**.

### Peer global ocean physics systems
- **[GLO12 (Mercator Océan / Copernicus Marine)](../france/glo12.md)** — NEMO-based ¹⁄₁₂° global ocean. Peer capability; GLO12 is a standalone forced ocean at higher resolution (¹⁄₁₂° vs ¼° here), whereas FOAM-GC is coupled to the atmosphere.
- **[Global RTOFS (NOAA)](../us/rtofs-global.md)** — HYCOM-based ¹⁄₁₂° global ocean; peer operational forecast.
- **Navy Global HYCOM (FNMOC)**, **BLUElink OceanMAPS (BoM)** — other peer operational global ocean systems.

FOAM-GC's distinguishing feature within this peer set is that it is delivered as the ocean component of an hourly-coupled atmosphere-land-ocean-ice forecast rather than as a standalone forced ocean.

### AI-based counterparts
- TBD — no AI counterpart trained specifically on FOAM-GC noted at time of writing.

---

## Notes
- **Coupled, not forced:** the single most important characterisation — the published ocean fields come from a model coupled hourly to the atmosphere, initialised by weakly-coupled DA.
- **Single published cycle:** only 00Z ocean products are distributed, despite the coupled system cycling 4×/day.
- **Convention drift:** live files are CF-1.8/ACDD-1.3; the December 2024 PUM (Issue 1.0) still states CF-1.7. Trust the file metadata.
- **Time coordinate:** `seconds since 1900-01-01 00:00:00`; daily-mean timestamps sit at the centre of the validity period. Files also carry CF `forecast_period` and `forecast_reference_time`.

---

## Official documentation
- Product User Manual: *Ocean Products User Guide, Met Office Global Coupled Atmosphere-Land-Ocean-Ice system*, Issue 1.0, December 2024 (Roberts-Jones, Price, Moreton, King).
- Key references: Xavier et al. (2024, GC4, https://doi.org/10.62998/uzui3766); Storkey et al. (2018, GO6, https://doi.org/10.5194/gmd-11-3187-2018); Ridley et al. (2018, sea ice, https://doi.org/10.5194/gmd-11-713-2018); Waters et al. (2015, DA, https://doi.org/10.1002/qj.2388); Barbosa Aguiar et al. (2024, FOAM, https://doi.org/10.1002/qj.4798); Lea et al. (2015, coupled DA, https://doi.org/10.1175/MWR-D-15-0174.1).
