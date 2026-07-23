# RESPS (Regional Ensemble Storm Surge Prediction System)

## What this model is
The **Regional Ensemble Storm Surge Prediction System (RESPS)** is Environment and Climate Change Canada's operational probabilistic storm surge forecast system for the Northwest Atlantic, run at the Canadian Centre for Meteorological and Environmental Prediction (CCMEP). It produces 16-day, 21-member forecasts of **storm surge elevation** and **total water level** twice daily over Atlantic Canada and the Gulf of Maine.

RESPS is built on **DalCoast 5** (Bernier and Thompson 2007, 2015), a depth-integrated, barotropic, linearized descendant of the Princeton Ocean Model developed at Dalhousie University. It is a conventional 2D shallow-water surge model — a very different design from its global sibling [GDSPS](../../global/canada/gdsps.md), which is a light-baroclinic NEMO configuration. The two systems together form ECCC's operational surge suite but share no model code.

The ensemble is driven by 10 m winds and sea level pressure from the 21 members of [GEPS](../../../ensemble_models/global/canada/geps.md). Distinctively, **the tidal boundary conditions are also perturbed for every member except the control**, so ensemble spread in the total water level field reflects tidal uncertainty as well as meteorological uncertainty — see the caution under *Ensemble configuration*.

Two products are distributed as one-file-per-timestep NetCDF-4 on the MSC Datamart, each containing all 21 members:
- **`ETAS`** — storm surge elevation (variable `etas`), from the surge-only model configuration
- **`SSH`** — total water level (variable `zos`), from the surge-with-tide configuration

The current operational version is **RESPS 1.8.0**, implemented **14 April 2026**.

---

## Who runs it
- **Organization:** Environment and Climate Change Canada (ECCC) / Canadian Centre for Meteorological and Environmental Prediction (CCMEP)
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Northwest Atlantic — Atlantic Canada, the Gulf of St. Lawrence, the Bay of Fundy, and the Gulf of Maine
- **Domain details:** 42.0°N–60.0°N, 288.0°E–315.7°E (72.0°W–44.3°W). Live-verified grid corners: latitude 42.00000° to 59.99993°, longitude 288.00000° to 315.66656°.
- **Open boundaries:** Head of the Bay of Fundy, head of the St. Lawrence River, and the northern, southern and eastern limits of the domain
- **Inundation coverage:** None. No wetting/drying or overland flooding is produced.
- **Masking:** 63.0% of grid points carry data (ocean); the remainder is land-masked. Unlike GDSPS, `etas` and `zos` share an identical mask.

---

## Basic details
- **Model type:** Ensemble storm surge / total water level model
- **Core hydrodynamic model:** DalCoast 5 — depth-integrated, barotropic, linearized Princeton Ocean Model derivative (Bernier and Thompson 2007, 2015)
- **Dimensionality:** True **2D barotropic**. Shallow water equations; independent variables are x, y and time only. No vertical levels, no temperature or salinity.
- **Horizontal resolution:** 1/12°
- **Time step:** External 12 s; internal 720 s
- **Forecast length:** **384 h (16 days)** — see the documentation caution in Notes
- **Update frequency / cycles:** 2× daily (00 and 12 UTC)
- **Temporal output resolution:** 1 h (forecast hours 000–384, all 385 steps distributed)

---

## Grid and bathymetry
- **Grid type:** Regular latitude–longitude, structured
- **Grid dimensions:** **333 (lon) × 217 (lat)**
- **Grid spacing (live-verified):** 1/12° in both directions — measured 0.083336° latitude and 0.083344° longitude, consistent with 0.0833333° to float32 precision. Published and used in filenames as `0.083`.
- **Physical spacing:** ~9.3 km in latitude; in longitude ~6.9 km at 42°N falling to ~5.2 km at 60°N. The `9km` path segment refers to the latitude spacing only — the cells are strongly anisotropic in metres.
- **Earth radius in grid mapping:** 6371229.0 m (note: GDSPS uses 6370997.0 m — the two ECCC surge products do not share an earth figure)
- **Bathymetry source:** ETOPO1 (Amante and Eakins 2009) with manual edits based on navigation charts
- **Wetting and drying:** No
- **Bottom stress:** Quadratic, bottom drag coefficient 2.5 × 10⁻³
- **Surface wind stress:** Quadratic with wind-dependent drag — 1.2 × 10⁻³ for W < 8 m s⁻¹, and (0.68 + 0.065 W) × 10⁻³ for W ≥ 8 m s⁻¹ (identical formulation to GDSPS)
- **Open boundary treatment:** Standard radiation condition (Davies and Flather 1978) at all open boundaries

---

## Vertical datum and reference level
- **Vertical datum:** Mean sea level, defined as the mean of the model's **0–12 h forecasts over the preceding 365 days** (minimum 90 days if a full year is unavailable). This is a model-internal datum, and its definition differs from GDSPS's — GDSPS averages its pseudo-analysis, RESPS averages its short-lead forecasts.
- **What the water level field is measured relative to:**
  - `zos` (SSH) = **total water level above the model mean**, including tide and surge
  - `etas` (ETAS) = **storm surge elevation**, produced by a separate surge-only run with no tidal forcing
  - **Tidal height is approximately `zos − etas`**, but see the caution below — this difference also absorbs nonlinear tide–surge interaction, and both fields are member-dependent
- **Datum conversion offsets provided?** No. No chart-datum, LAT, or geoid offset is distributed.
- **Sea level rise handling:** Not addressed. The trailing 365-day mean drifts slowly with the model climate rather than being pinned to a fixed epoch.

> **Live-verified caution — misleading CF metadata.** The `zos` variable carries both `standard_name` **and** `long_name` set to `sea_surface_height_above_geoid`, but the technical specification's *Levelling* row states the field is levelled to the 365-day mean of the model's own short-lead forecasts, **not to the geoid**. This repeats the same defect found in GDSPS, where at least the `long_name` and `description` attributes corrected the record; in RESPS there is no corrective attribute in the file at all. Treat the datum as model mean sea level.

---

## Tide handling
- **Are tides included?** Both configurations are run. `ETAS` comes from a **surge-only** run with no tidal forcing; `SSH` comes from a **surge-with-tide** run. This is a cleaner separation than GDSPS, which runs tides once and removes them afterward by harmonic analysis.
- **Tidal forcing source:** Tidal currents and scaled tidal heights applied at all open boundaries, taken from the **WebTide Northwest Atlantic** tidal model (Dupont et al. 2002).
- **Separation of components:** Surge and total water level are distributed directly. The tide is not distributed as its own product.
- **Tide–surge interaction:** Represented in the surge-with-tide run. Because the two configurations are separate integrations, `zos − etas` is *tide plus the nonlinear interaction term*, not a pure tidal prediction.
- **Tidal perturbation:** The tidal boundary conditions are **perturbed independently for each ensemble member except the control** (introduced in v1.4.0). This is unusual — most operational surge ensembles inherit spread solely from the driving atmospheric ensemble.

---

## Forcing and coupling
- **Meteorological forcing — wind:** 10 m winds (`UU`, `VV`) from [GEPS](../../../ensemble_models/global/canada/geps.md), stream E1
- **Meteorological forcing — pressure:** GEPS sea level pressure (`PN`)
- **Forcing frequency:** Hourly, including for lead times between 168 h and 384 h (extended to hourly at those leads in v1.5.0)
- **Driving ensemble version:** GEPS **8.0.0**
- **Wave contribution:** None. RESPS is not coupled to a wave model and does not include wave setup.
- **River discharge / freshwater forcing:** Not used. The St. Lawrence enters as an open boundary with a radiation condition, not as a discharge forcing.
- **Ocean forcing:** None. RESPS is barotropic and does not ingest temperature, salinity, or baroclinic ocean state — a significant design difference from GDSPS, which nudges to GIOPS.
- **Ice forcing:** None documented. RESPS has no ice–ocean stress parameterization, unlike GDSPS.
- **Input interpolation:** Linear in time; linear aggregation-interpolation in space

---

## Initialization and data assimilation
- **Assimilates water level observations:** **No.** RESPS has no data assimilation system.
- **Initialization:** Each forecast restarts from the **previous cycle's 12 h lead time** restart file — the 00 UTC forecast from the previous day's 12 UTC run, and the 12 UTC forecast from that day's 00 UTC run. This is a continuously cycled model state rather than a fresh analysis.
- **Forcing blending:** To avoid a discontinuity in atmospheric forcing at cycle changeover, winds and pressure are blended over the **first 6 hours** of each forecast with the previous cycle's forcing at lead times 12 h to 18 h.

---

## Ensemble configuration
- **Ensemble size:** 21 — 1 control plus 20 perturbed members
- **Member packaging:** All members are contained in **a single file per timestep** via a `member` dimension of length 21, with a CF `realization` coordinate variable. Live-verified `realization` values are the integers **0 through 20**, where **0 is the control**. Field shape is `(time_counter, member, lat, lon)` = `(1, 21, 217, 333)`.
- **Source of perturbations:** Two independent sources —
  1. **Meteorological:** members map one-to-one onto the 21 members of GEPS 8.0.0 (1 control + 20 perturbed), inheriting that system's initial-condition and stochastic physics perturbations
  2. **Tidal:** the tidal open-boundary conditions are perturbed separately for each member except the control
- **Deterministic counterpart:** None at the regional scale. The nearest sibling is the global deterministic [GDSPS](../../global/canada/gdsps.md), which differs in domain, model core, and physics — RESPS is *not* an ensemble version of GDSPS.
- **Derived products distributed:** None. Raw members only; no ensemble mean, spread, percentile, or exceedance-probability fields are published on the Datamart. Users must compute these themselves across the `member` dimension.

> **Live-verified caution — SSH spread is dominated by tidal perturbation at short and medium leads.** Because the tidal boundary conditions are perturbed per member, ensemble spread in `zos` is *not* a clean measure of meteorological forecast uncertainty. Measured on the 2026-07-22 12 UTC run (domain-mean across-member standard deviation, metres):
>
> | Lead | `etas` spread (surge) | `zos` spread (total) | implied tidal spread |
> |------|----------------------|----------------------|----------------------|
> | 024 h | 0.0085 | 0.0615 | 0.0613 |
> | 120 h | 0.0293 | 0.0961 | 0.0906 |
> | 240 h | 0.0557 | 0.0936 | 0.0791 |
> | 384 h | 0.0632 | 0.0960 | 0.0581 |
>
> At day 1 the tidal component of spread exceeds the meteorological component by roughly a factor of seven, and the two only become comparable near day 16. For probabilistic assessment of *storm surge* uncertainty, use `etas`. Use `zos` spread only when tidal uncertainty is genuinely part of the question being asked.

---

## What it provides
- **Storm surge elevation** (`etas`, m) — 21 members, from the surge-only configuration
- **Total water level** (`zos`, m, relative to model mean sea level) — 21 members, from the surge-with-tide configuration

No currents, no inundation depth, no peak-envelope product, and no station time series. Output is gridded only.

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required. Suggested attribution: "Data Source: Environment and Climate Change Canada." https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded fields only
- **Data formats:** NetCDF-4 (HDF5 container), zlib level 4 with shuffle, chunked `[1, 1, 55, 111]` (one member, one quarter-tile per chunk)
- **Official download location:**
  - Current day: https://dd.weather.gc.ca/today/model_resps/atlantic-nw/9km
  - Dated archive: `https://dd.weather.gc.ca/{YYYYMMDD}/WXO-DD/model_resps/atlantic-nw/9km/{HH}/`
  - where `HH` is `00` or `12`
- **File naming:** `{YYYYMMDD}T{HH}Z_MSC_RESPS-Atlantic-North-West_{VAR}_Sfc_LatLon0.083_PT{hhh}H.nc`, with `VAR` in `{ETAS, SSH}` and `hhh` from `000` to `384`
- **Files per run:** 770 (385 `ETAS` + 385 `SSH`) — live-confirmed on the 2026-07-22 12 UTC run
- **File size:** `ETAS` ~1.7–1.8 MiB, `SSH` ~1.5–1.7 MiB; approximately **1.27 GiB per run**
- **Retention:** Dated directories persist for **30 days** (live-probed: 2026-06-23 present, 2026-06-22 returns 404) — same policy as GDSPS
- **Publication latency:** The full run is written in a single burst roughly **T+5h00m** after cycle time (the 2026-07-22 12 UTC run was written at 17:01 UTC), about 40 minutes ahead of GDSPS
- **Push notification:** Available via AMQP (MSC Datamart sr3/sarracenia)
- **Other access:** The same fields are served through MSC GeoMet as WMS and WCS layers.

---

## Notes
- **The datamart documentation understates the forecast length.** The file-nomenclature section gives the forecast hour range as `[000, 001, 002, ..., 240]`, but the system has produced **384-hour** forecasts since v1.4.0 (November 2020). Live-confirmed: 385 distinct forecast hours per variable, out to `PT384H`. The factsheet and technical specification correctly state 16 days; the datamart page is stale.
- **The datamart "Variable list" section is empty.** Unlike the GDSPS page, which documents the datum and the `zos − etas` relationship, RESPS ships no variable documentation on its Datamart page at all. Everything in this entry's variable, datum and masking description comes from the technical specification plus direct file inspection.
- **The files carry no global attributes.** There is no `Conventions`, `title`, `institution`, `source`, or `creation_date` attribute in RESPS NetCDF output — so, unlike GDSPS (whose files stamp `source: GDSPS/SGPDOT 2.3.0, forecast`), **there is no in-file record of which system version produced a given file**. Users archiving RESPS data should record the version externally.
- **Fill-value handling is inconsistent between the two products.** `etas` declares only `missing_value = 1e30` with no `_FillValue`; `zos` declares both `_FillValue` and `missing_value = 1e20`. The sentinel values differ between the two files. Readers that key on `_FillValue` alone will fail to mask `etas`.
- **`etas` has no CF `standard_name`,** only `long_name = storm_surge`. GDSPS's equivalent field does carry `standard_name = non_tidal_elevation_of_sea_surface_height`. The two ECCC surge products are not metadata-consistent with each other.
- **Time encoding differs from GDSPS.** RESPS uses a `time_counter` dimension with units `seconds since 1950-01-01`; GDSPS uses `time` with units `hours since <run start>`. Code written against one will not transfer unmodified to the other.
- **The `9km` path segment refers to latitude spacing only.** At the domain's latitudes the cells are roughly 9.3 km north–south but only 5.2–6.9 km east–west.
- **Relationship to other ECCC systems:**
  - Driving ensemble: [GEPS](../../../ensemble_models/global/canada/geps.md) 8.0.0 supplies 10 m wind and MSLP for all 21 members. RESPS member *n* corresponds to GEPS member *n*.
  - Global surge sibling: [GDSPS](../../global/canada/gdsps.md). The pair covers global deterministic and regional probabilistic surge respectively; they are **not** a deterministic/ensemble pair of one system.
  - Regional ocean physics for the same waters: [RIOPS](../../../ocean_models/regional/canada/riops.md), which is baroclinic and ice-coupled where RESPS is barotropic.
  - Shared lineage: both ECCC surge systems trace to Bernier and Thompson's Atlantic Canada surge work, and both use the same wind-stress drag formulation and bottom drag coefficient.
- **Experimental history.** RESPS ran with experimental status until v1.5.0 (1 December 2021), when it was promoted to operational. Data from before that date should be treated accordingly.

---

## Recent version history

All RESPS versions have been implemented at the **12 UTC run** of the stated date.

### RESPS 1.8.0 — operational 14 April 2026 (current)
Adaptation to ECCC's new High Performance Computing infrastructure. Infrastructure-only; no scientific or configuration changes. Part of the same multi-system migration that produced GDSPS 2.2.0 and [RIOPS](../../../ocean_models/regional/canada/riops.md) v2.5.0 on the same date.

### RESPS 1.7.0 — operational 11 June 2024
Single change: wind and sea level pressure forcing upgraded to **GEPS 8.0.0**. Storm surge CRPS improved at days 5–7 in the March–May 2024 parallel evaluation, though the improvement was not reproduced in the March–May 2022 final-cycle comparison; forecasts otherwise of equivalent quality.

### RESPS 1.6.0 — operational 28 June 2022
Adaptation to the High Performance Computing infrastructure of the time. Infrastructure-only.

### RESPS 1.5.0 — operational 1 December 2021
- Forcing upgraded to **GEPS 7.0.0**
- Hourly forcing extended to lead times between 168 h and 384 h
- **Status changed from experimental to operational**

### RESPS 1.4.0 — operational 24 November 2020
- Added **total water level (`SSH`) forecasts based on perturbed tidal forcing** — the origin of the system's dual surge-only / surge-with-tide configuration and of the per-member tidal perturbation
- Forecast range extended to **16 days**

### RESPS 1.3.0 — operational 21 January 2020
Adaptation to the High Performance Computing infrastructure of the time. Infrastructure-only.

### RESPS 1.2.0 — operational 3 July 2019
Pilot system upgraded from GEPS 5.0.0 to **GEPS 6.0.0**. This is the earliest version covered by the published changelog; earlier versions ran with experimental status and are not documented in the open data record.

> **Note on dating.** As with GDSPS, the technical specification's document-history table records *document revision* dates rather than implementation dates — its v1.3 entry is dated 8 December 2023 for a version implemented 11 June 2024. The changelog is the authoritative source for operational dates.

---

## Official documentation
- RESPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_resps/readme_resps_en/
- RESPS Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_resps/readme_resps-datamart_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RESPS_e.pdf
- Technical specifications (v1.7.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RESPS_1.7.0_e.pdf
- Technical specifications (v1.5.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_specifications/tech_specifications_RESPS_1.5.0_e.pdf
- Technical specifications (v1.2.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RESPS_1.2.0_e.pdf
- Technical note (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_notes/technote_resps_e.pdf
- Technical note (v1.7.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_resps-170_e.pdf
- Factsheet (current, v1.7.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_resps_e.pdf
- Factsheet (v1.7.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_resps-170_e.pdf
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_RESPS_en.svg
- RESPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_resps/changelog_resps_en/
- RESPS grid figure: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwp_resps/grille_resps.png
- Open Government Portal metadata: https://open.canada.ca/data/en/dataset/2c2cadd7-5248-4764-bf88-5042b73465c3
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Bernier, N.B., and K.R. Thompson (2015). Deterministic and ensemble storm surge prediction for Atlantic Canada with lead times of hours to ten days. *Ocean Modelling*, 86, 114–127. https://doi.org/10.1016/j.ocemod.2014.12.002
- Bernier, N.B., and K.R. Thompson (2007). Tide-surge interaction off the east coast of Canada and northeastern United States. *J. Geophys. Res.*, 112, C06008. https://doi.org/10.1029/2006JC003793
- Dupont, F., C.G. Hannah, D.A. Greenberg, J.Y. Cherniawsky, C.E. Naimie (2002). Modelling System for Tides. *Can. Tech. Rep. Hydrogr. Ocean Sci.* 221, vii + 72 pp.
- Davies, A.M., and R.A. Flather (1978). Application of numerical models of the north west European continental shelf and the North Sea to the computation of the storm surges of November to December 1973. *Dtsch. Hydrogr. Z.*, Erganzungsheft A, 14, 72 pp.
- Amante, C., and B.W. Eakins (2009). ETOPO1 1 Arc-Minute Global Relief Model: Procedures, data sources and analysis. *NOAA Tech. Memo.* NESDIS NGDC-24, 19 pp.
