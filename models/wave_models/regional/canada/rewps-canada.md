# REWPS (Regional Ensemble Wave Prediction System)

## What this model is
The Regional Ensemble Wave Prediction System (REWPS) is Canada's operational **ensemble** wave forecasting system for the Great Lakes, producing probabilistic wave forecasts out to 3 days (72 hours).

REWPS is the ensemble sibling of the deterministic [RDWPS](./rdwps-canada.md), but covers only the Great Lakes — not the Atlantic and Pacific ocean domains that RDWPS also runs. It runs the WAVEWATCH III® spectral wave model as a 21-member ensemble, with each member forced by the corresponding member of the [Regional Ensemble Prediction System (REPS)](../../../ensemble_models/regional/canada/reps.md), so the wave-forecast spread is inherited from the driving atmospheric ensemble.

The current operational version is **REWPS 1.8.0** (an HPC-infrastructure port of the scientific configuration 1.7.0), implemented 14 April 2026.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Canadian Centre for Meteorological and Environmental Prediction (CCMEP), Environment and Climate Change Canada (ECCC)
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** The Great Lakes (single combined domain)
- **Distributed grid:** Regular latitude–longitude, **550 × 365** points, ~**0.031° lon × 0.0224° lat** (~2.5 km), spanning 41.10°–49.25°N and 267.52°–284.54°E (267.52°E = 92.48°W). First grid point 41.0984°N 92.4790°W. Live-verified on the 2026-07-24 00 UTC run (`centre: cwao`). Unlike [RDWPS](./rdwps-canada.md), which distributes each lake as its own ~1 km grid, REWPS distributes all lakes together on one ~2.5 km grid.

---

## Basic details
- **Model type:** Ensemble wave model
- **Grid system:** Single regular lat-lon grid covering all the Great Lakes
- **Core wave model:** WAVEWATCH III® (WW3) version 7.0
- **Spectral resolution:** 36 directional bins (10° each) × 40 frequency bins from 0.050 Hz to 1.0058 Hz (increment factor 1.08), with a parametric tail at higher frequencies
- **Horizontal resolution:** ~2.5 km
- **Forecast length:** 72 hours (3 days)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 3-hourly (000–072 h; 25 time steps, live-verified)

---

## Forcing and nesting
- **Wind forcing:** REPS 10 m winds (`UU`, `VV`), version 5.0.0, hourly — each REWPS member forced by the matching REPS member
- **Ice forcing:** ice concentration (`GL`) from the deterministic WCPS (Water Cycle Prediction System) version 3.3.0, hourly — common to all members. Wave growth is dampened where ice is 25–75% and suppressed above 75%.
- **Current forcing:** None
- **Nested inside / parent for:** Shares the Great Lakes WW3 configuration with [RDWPS](./rdwps-canada.md) (same spectral setup, same NGDC bathymetry); the Great Lakes are closed basins, so no external wave boundary conditions are needed.

---

## Ensemble configuration
- **Ensemble size:** 21 members — 1 control + 20 perturbed
- **Source of perturbations:** Inherited from the [REPS](../../../ensemble_models/regional/canada/reps.md) atmospheric ensemble (perturbed 10 m winds per member). The wave model itself is not separately perturbed. Note the WCPS ice forcing is a single deterministic field shared across all members, so the spread comes from the REPS winds alone.
- **Resolution / output differences vs deterministic sibling:** Coarser and less frequent than [RDWPS](./rdwps-canada.md) over the lakes — one combined ~2.5 km grid vs RDWPS's per-lake ~1 km grids, 3-hourly vs RDWPS's hourly — but a longer 72-hour horizon vs RDWPS's 48 hours, and probabilistic rather than single-valued.
- **Member packaging:** One GRIB2 file per variable per time step, containing **all 21 members as separate GRIB messages** — no member token in the filename. Live-verified encoding: GRIB2 Product Definition Template 4.1, `numberOfForecastsInEnsemble = 21`, `perturbationNumber` 0–20 (0 = control, 1–20 = perturbed), `centre = cwao`. A single `HTSGW` time-step file is ~1.7 MB.
- **Derived products distributed:** None as raw GRIB — the Datamart ships the raw members only. Ensemble mean, spread, and probability/percentile products are available through ECCC's GeoMet-Weather web services or must be computed by the user from the members.

---

## Data assimilation
- **Assimilates wave observations:** No. REWPS has no wave data assimilation system.
- **Initialization:** Member-continuous cycling rather than a pseudo-analysis. Each member writes a restart file at the 6-hour mark, and the next run of that same member restarts from it — e.g. the 00 UTC forecast starts from the previous day's 18 UTC forecast at 6-hour lead time.

---

## What it provides
Probabilistic Great Lakes wave forecasts (surface, GRIB2, all 21 members per file). Live-verified field list on the 2026-07-24 00 UTC run (13 wave fields — a smaller set than RDWPS/GEWPS: no surface Stokes drift, no mean wave direction, no 10 m wind, no ice):

- **Combined sea state:** significant height of combined wind waves and swell (`HTSGW`)
- **Wind-sea partition:** significant height (`WVHGT`), direction (`WVDIR`), peak period (`PPERWW`)
- **Swell partitions (first and second):** significant height (`SWHFSWEL`, `SWHSSWEL`), mean direction (`MWDFSWEL`, `MWDSSWEL`), peak period (`PWPFSWEL`, `PWPSSWEL`)
- **Total-spectrum period / direction:** peak wave period (`PWPER`), mean zero-crossing period (`MZWPER`), peak wave direction (`PWAVEDIR`)

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTP)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (21 ensemble members per file)
- **Official download location:**
  https://dd.weather.gc.ca/today/model_rewps/great-lakes/grib2/
  - **Path template:** `https://dd.weather.gc.ca/today/model_rewps/great-lakes/grib2/{HH}/` — `{HH}` = run (`00`/`06`/`12`/`18`)
  - **Filename convention:** `{YYYYMMDD}T{HH}Z_MSC_REWPS-Great-Lakes_{VAR}_Sfc_LatLon0.022x0.031_PT{hhh}H.grib2` (e.g. `20260724T00Z_MSC_REWPS-Great-Lakes_HTSGW_Sfc_LatLon0.022x0.031_PT066H.grib2`) — members are inside the file, not in the name
  - Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_rewps/readme_rewps-datamart_en/

---

## Notes
- **Great Lakes only.** REWPS is the ensemble counterpart to the *Great Lakes portion* of RDWPS. There is no ensemble equivalent of RDWPS's Northeast Pacific or Northwest Atlantic ocean domains.
- **Raw members, not probabilities.** Users wanting exceedance probabilities, percentiles, or ensemble mean/spread must derive them from the 21 members (or use GeoMet-Weather). This is the same phenomenon-over-ensemble-status packaging used elsewhere in the repository for marine ensembles.
- **Tech-spec resolution typo.** The v1.7.0 technical specification lists the horizontal resolution as "2.5 km (0.0124 × 0.0090 degree)", but those degree increments are RDWPS's 1 km values. The live-distributed grid is 550 × 365 at ~0.031° lon × 0.0224° lat, consistent with the "2.5 km" label; treat the increment figures in the spec as a copy-paste error.
- **Model physics:** input/dissipation source terms ST4 (Ardhuin et al. 2010); DIA nonlinear interactions; JONSWAP bottom friction; Battjes–Janssen depth-induced breaking; third-order QUICKEST propagation with ULTIMATE TVD limiter. Bathymetry from NGDC Great Lakes grids; coastline from GSHHS. Overall time step 240 s. The Great Lakes spectral and physics setup matches RDWPS's lake domains; the difference is the REPS ensemble forcing.
- **Relationship to other ECCC systems:**
  - Deterministic sibling: [RDWPS](./rdwps-canada.md) — same Great Lakes WW3 configuration, higher resolution, shorter horizon, single-valued.
  - Global ensemble counterpart: [GEWPS](../../global/canada/gewps-canada.md) — ECCC's global ensemble wave system.
  - Atmospheric driver: [REPS](../../../ensemble_models/regional/canada/reps.md) 5.0.0 (per-member 10 m winds).
  - Ice driver: WCPS (Water Cycle Prediction System) 3.3.0 *(entry pending)*.

---

## Recent version history

Versions are implemented at the stated date's 12 UTC run.

### REWPS 1.8.0 — operational 14 April 2026 (current)
Adaptation to ECCC's new High Performance Computing infrastructure. Infrastructure-only; no scientific or configuration changes. The v1.7.0 technical specification still describes the current scientific configuration.

### REWPS 1.7.0 — operational 11 June 2024
Wind forcing moved to REPS 5.0.0; ice forcing to WCPS 3.3.0; activated a Miche-style shallow-water limiter; **added the 06 and 18 UTC forecasts** (previously 2× daily).

### REWPS 1.6.0 — operational 28 June 2022
HPC-infrastructure adaptation.

### REWPS 1.5.0 — operational 1 December 2021
Adopted WAVEWATCH III v7; REPS 4.0.0 and WCPS 3.0.0 forcing.

### REWPS 1.4.0 — operational 21 January 2020
HPC-infrastructure adaptation.

### REWPS 1.2.0 — operational 4 March 2019
Ice input switched from an analysis to a WCPS (v2.0.0) ice forecast.

### REWPS 1.0.0 — declared operational 4 April 2018
Initial operational implementation at CMC.

---

## Official documentation
- REWPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_rewps/readme_rewps_en/
- REWPS Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_rewps/readme_rewps-datamart_en/
- Technical specifications (current, describes v1.7.0 config): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_REWPS_e.pdf
- Technical specifications (v1.7.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_REWPS_1.7.0_e.pdf
- Technical note (v1.7.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_rewps-170_e.pdf
- Factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_rewps_e.pdf
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_REWPS_en.svg
- REWPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_rewps/changelog_rewps_en/
- Open Government Portal metadata: https://open.canada.ca/data/en/dataset/a0e5c7a1-03df-413b-9b04-8e9d41099c19
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/
