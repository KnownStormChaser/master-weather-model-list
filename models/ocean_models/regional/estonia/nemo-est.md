# NEMO-EST (Estonian Regional Ocean Model)

## What this model is
NEMO-EST is a regional operational ocean-physics forecasting system for the Estonian sea areas — the Gulf of Finland, Gulf of Riga, Väinameri, and the adjacent eastern Baltic Proper. It is a configuration of **NEMO (Nucleus for European Modelling of the Ocean) v4.0**, adapted and tuned to Estonian conditions.

The system is a standalone (forced) 3D hydrodynamic–sea-ice model: it provides forecasts of temperature, salinity, currents, sea level, and sea ice. It runs the NEMO **OCE** (ocean dynamics) and **SI3** (sea ice) modules; the biogeochemistry module (**TOP**) and the ERGOM ecosystem model are part of the configuration but are **not activated** in the operational setup.

---

## Who runs it
- **Production Unit:** Estonian Environment Agency (Keskkonnaagentuur), through its national weather service, the Estonian Weather Service (Riigi Ilmateenistus, ilmateenistus.ee)
- **Country:** Estonia
- **Model development / adaptation:** Configured and adapted to Estonian conditions by the **Marine Systems Institute of Tallinn University of Technology (TalTech / TTÜ MSI)**
- **Programme or coordinating body:** Operational enhancements to the underlying NEMO configuration are drawn from the Copernicus Marine **Baltic Monitoring and Forecasting Centre (BAL MFC)** modelling group (Kärnä et al., 2021)
- **Role in any larger system:** Standalone regional ocean forecast; also provides the ocean fields underlying the agency's public marine forecast products (currents, sea level, sea temperature, salinity). It supplies ocean context alongside the [SWAN-EST](../../../wave_models/regional/estonia/swan-est.md) wave model.

---

## What area it covers
- **Coverage:** Estonian sea areas — Gulf of Finland, Gulf of Riga, Väinameri (West Estonian Archipelago Sea), and the adjacent eastern Baltic Proper along the Estonian coast
- **Domain bounds (from live files):** 21.55°E – 30.35°E, 56.94°N – 60.72°N
- **Grid dimensions:** 529 (x) × 455 (y) — the same horizontal footprint as the companion SWAN-EST wave model
- **Special masked or excluded regions:** Land points are fill-masked; the NEMO Arakawa **C-grid** stores separate T/U/V/W sub-grids, each with its own 2D `nav_lat`/`nav_lon` coordinates and depth axis

---

## Basic details
- **Model type:** Regional deterministic ocean physics + sea ice, standalone (one-way atmospheric forcing)
- **Core ocean model:** NEMO v4.0 (OCE module; operational enhancements from the Copernicus BAL MFC group, Kärnä et al., 2021)
- **Sea ice model:** SI3 (Sea Ice modelling Integrated Initiative; Aksenov et al., 2019) — active
- **System name:** NEMO-EST (file token `EST05nm`, internal operational suite `NEMO_EST_0.5nm_op`)
- **Horizontal resolution:** 0.5 nautical mile (~0.9 km); ≈0.0167° longitude × 0.0083° latitude
- **Vertical levels:** 110 z-levels, from 0.5 m to ~136 m depth; ~1 m spacing near the surface
- **Vertical coordinate:** Z-level (fixed-depth). Partial-step / z* treatment not documented (TBD)
- **Forecast length:** 120 hours (5 days) — observed as five daily `stuvw` files per run
- **Update frequency:** 2× daily
- **Production cycles:** 00 and 12 UTC (the cycle hour is encoded in the `nemo_YYYYMMDDHH_…` filename token; the `run1` token is constant and does not distinguish cycles)
- **Target delivery time:** ~07:00 UTC for the 00 UTC cycle (data files internally timestamped ~07:05 UTC); the 12 UTC cycle is published ~12 h later
- **Temporal output resolution:** Hourly (24 steps per daily file)
- **Archive availability:** Rolling — approximately the most recent month of runs is retained (consistent with the portal's other datasets; not independently confirmed for NEMO-EST) (TBD)
- **Bathymetry source:** Not documented (TBD)

> **Note on run frequency and forecast length:** The portal's data-description page (last updated 2024, describing the 2023 configuration) states NEMO-EST runs **once** daily producing three data files covering 0–24 h, 24–48 h, and 48–72 h (72 h total). The live 2026 file listing shows the system now runs **twice** daily (00 and 12 UTC), each cycle producing **five** daily files out to 120 h — both the run frequency and the forecast length have been extended since that description was written.

---

## Forcing
- **Atmospheric forcing:** ECMWF meteorology (10 m winds `u10`/`v10`, wind speed, and surface wind stress `utau`/`vtau` are carried in the accompanying coordinate/surface file). Estonia is a full ECMWF member state and the suite runs on ECMWF HPC.
- **River runoff:** Included — rivers are listed among the model's dynamic inputs; whether climatological or dynamic is not documented (TBD)
- **Lateral boundary conditions:** Copernicus Marine operational Baltic Sea model (BAL MFC product), updated per cycle
- **Tidal forcing:** Not documented; the Baltic is microtidal, so tidal contribution is minor (TBD)
- **Ice forcing or coupling:** Sea ice is modelled internally by the SI3 module (not externally forced)
- **Initial conditions:** Operational restart chain (previous cycle's state), with ECMWF meteorology and Copernicus boundary conditions ingested at the start of each run. No independent data assimilation is documented for NEMO-EST itself (TBD)

---

## Coupling
- **Standalone ocean physics + sea ice:** NEMO-EST is not two-way coupled to an atmospheric model. It receives ECMWF atmospheric fields as one-way surface forcing and Copernicus Baltic fields as lateral open-boundary conditions. Ocean and sea ice (OCE + SI3) are coupled internally within NEMO.

---

## What it provides
**3D fields** (time × 110 depth levels × y × x):
- `temperature` — sea water potential temperature (°C)
- `salinity` — sea water practical salinity (PSU)
- `uos` / `vos` — horizontal current components, x/y (m/s)
- `wos` — vertical current velocity (m/s)

**2D surface fields** (time × y × x):
- `SST` — sea surface temperature (°C)
- `SSS` — sea surface salinity (PSU)
- `SSH` — sea surface height above geoid (m)
- `SSU` / `SSV` — surface current components (m/s)
- `icethic` — sea ice thickness (m); `icefrac` — sea ice area fraction; `uice` / `vice` — sea ice drift velocity (m/s); `snwthic` — snow thickness on ice (m)

The companion `SURF_grid_TUV` coordinate/surface file additionally carries the applied atmospheric forcing (`u10`, `v10`, `windspeed`, `utau`, `vtau`) and the regular 1D `lon`/`lat` axes.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC-BY 4.0) — https://creativecommons.org/licenses/by/4.0/ (attribution: cite Keskkonnaagentuur / Estonian Environment Agency). *Note:* CC-BY 4.0 covers the **data**; the NEMO model software itself is licensed under CeCILL (a French GPL-compatible license) — this is a code license, not a data-use restriction.
- **Access rights:** Public ("Avalik")
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 / HDF5 (CF-1.6), internally deflate-compressed (`zip2`)
- **File structure — two files per forecast day are required:**
  - Data file: `nemo_YYYYMMDD00_EST05nm_op_run1_1h_stuvw_<startdate>-<enddate>.nc` (~1.26 GB each)
  - Coordinate/surface file: `nemo_YYYYMMDD00_EST05nm_op_run1_1h_SURF_grid_TUV.<date>.nc` (~75 MB each)
  - The portal explicitly instructs users to download **both** the `stuvw` data file and the matching-date `SURF_grid_TUV` file for correct processing.
- **Browsable file list (portal UI):**
  https://avaandmed.keskkonnaportaal.ee/dhs/Active/documentList.aspx?ViewId=40a63be9-52d3-4833-b9f6-1d6617a8fff5
- **Direct file-download pattern:**
  `https://avaandmed.keskkonnaportaal.ee/dhs/Active/_layouts/RM/Content.aspx?ListId=228b4970-73de-44a4-83e1-9513be360001&ID=<documentId>&fileId=1`
  (NEMO-EST and SWAN-EST share the same records list `228b4970-…`, separated by view.)
- **KAIA machine API (SharePoint RmApi):**
  - Metadata query (POST): `https://avaandmed.keskkonnaportaal.ee/_vti_bin/RmApi.svc/active/items/query`
  - Zipped files (POST): `https://avaandmed.keskkonnaportaal.ee/_vti_bin/RmApi.svc/active/items/zipped-files`
  - Body filters on content type, e.g. `{"filter":{"isEqual":{"field":"$contentType","value":"<contentType>"}}}` (the NEMO-EST-specific `$contentType` is not exposed on the public view page — TBD).

---

## Notes
- NEMO-EST and [SWAN-EST](../../../wave_models/regional/estonia/swan-est.md) run on the **same 529×455 horizontal grid** and are distributed together on the Environment Agency's model-forecast page. NEMO supplies the ocean state (currents, sea level, temperature, salinity, ice); SWAN supplies the wind-wave state.
- The distributed files are large: each daily `stuvw` file is ~1.26 GB (110 levels × 24 hours × ~240k horizontal points, five per run). Byte-range HDF5 reads or server-side subsetting are advisable for targeted use.
- Sea ice fields are present year-round but are zero over ice-free summer months (verified: `icefrac = 0` in the 25 July run).
- The operational chain runs on ECMWF HPC and can be interrupted if compute resources or input data (ECMWF meteo, Copernicus boundaries) are delayed — the portal documents wait tolerances of 45 min (meteo) and 30 min (boundaries) before a run may be skipped, so occasional cycle gaps are possible.
- Pre-operational since 24 September 2021; validated against satellite, FerryBox, and tide-gauge observations over 24 Sep – 15 Nov 2021 (surface temperature bias generally ±1 °C; surface salinity ±1 PSU).

---

## Official documentation
- Dataset page (Ilma mudelprognoosid): https://keskkonnaportaal.ee/et/avaandmed/ilma-mudelprognoosid
- Data description & usage (Ilma mudeliandmete kirjeldus): https://keskkonnaportaal.ee/et/avaandmed/ilma-mudelprognoosid/ilma-mudeliandmete-kirjeldus
- Visualized marine products: https://www.ilmateenistus.ee/meri/mereprognoosid/tuule-kiirus-ja-suund/
- Access rights & licensing description: https://keskkonnaportaal.ee/et/avaandmed#Juurdepsuigusetekirjeldusandmestikejuures
- NEMO ocean model: https://www.nemo-ocean.eu/

---

## References
- Kärnä, T., et al. (2021). A baroclinic discontinuous Galerkin finite element model for coastal flows / Copernicus BAL MFC modelling developments.
- Madec, G., et al. (1998, and NEMO team). *NEMO ocean engine*. Institut Pierre-Simon Laplace.
- Aksenov, Y., et al. (2019). SI3 sea-ice model documentation.
- NEMO-EST validation reports (Estonian Environment Agency): NEMO_II_aruanne2021, NEMO aruanne 2022 (III etapp).
