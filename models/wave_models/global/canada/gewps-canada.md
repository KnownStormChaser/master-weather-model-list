# GEWPS (Global Ensemble Wave Prediction System)

## What this model is
The Global Ensemble Wave Prediction System (GEWPS) is Canada's operational global **ensemble** wave forecasting system, producing probabilistic ocean-wave forecasts worldwide out to 16 days (384 hours).

GEWPS is the ensemble sibling of the deterministic [GDWPS](./gdwps-canada.md). It runs the WAVEWATCH III® spectral wave model as a 21-member ensemble, with each member forced by the corresponding member of the [Global Ensemble Prediction System (GEPS)](../../../ensemble_models/global/canada/geps.md) — so the wave-forecast spread is inherited almost entirely from the driving atmospheric ensemble rather than from perturbing the wave model itself.

The current operational version is **GEWPS 1.4.0** (an HPC-infrastructure port of the scientific configuration 1.3.0), implemented 14 April 2026.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Canadian Centre for Meteorological and Environmental Prediction (CCMEP), Environment and Climate Change Canada (ECCC)
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global oceans
- **Distributed grid:** Single global regular latitude–longitude grid, **1440 × 721** points, **0.25°**, first grid point 90°S 0°E (spans 90°S→90°N, 0°→359.75°E). Live-verified on the 2026-07-24 00 UTC run (`centre: cwao`).
- **Computational domain:** Yin–Yang overlapping grids at ~39 km, interpolated onto the 0.25° distributed grid. The distribution grid is finer than the model actually resolves.

---

## Basic details
- **Model type:** Ensemble wave model
- **Grid system:** Yin–Yang overlapping computational grids; single global 0.25° regular lat-lon grid on distribution
- **Core wave model:** WAVEWATCH III® (WW3) version 7.0
- **Spectral resolution:** 36 directional bins (10° each) × 36 frequency bins from 0.035 Hz to 0.984 Hz (increment factor 1.10), with a parametric tail at higher frequencies
- **Horizontal resolution:** ~39 km computational; 0.25° distributed
- **Forecast length:** 384 hours (16 days)
- **Update frequency / cycles:** 2× daily (00, 12 UTC)
- **Temporal output resolution:** 3-hourly (000–384 h; 129 time steps, live-verified)

---

## Forcing and nesting
- **Wind forcing:** GEPS 10 m winds (`UU`, `VV`), version 8.0.0, hourly — each GEWPS member forced by the matching GEPS member
- **Ice forcing:** GEPS sea-ice concentration (`GL`), version 8.0.0, 3-hourly. Wave growth is dampened where ice is 25–75% and suppressed above 75%.
- **Current forcing:** None
- **Nested inside / parent for:** Shares physics and grid lineage with [GDWPS](./gdwps-canada.md) but is a self-contained global system (not nested).

---

## Ensemble configuration
- **Ensemble size:** 21 members — 1 control + 20 perturbed
- **Source of perturbations:** Inherited from the [GEPS](../../../ensemble_models/global/canada/geps.md) atmospheric ensemble (perturbed 10 m winds and ice per member). The wave model itself is not separately perturbed; GEWPS translates atmospheric-ensemble spread into wave-forecast spread.
- **Resolution / output differences vs deterministic sibling:** Coarser and less frequent than [GDWPS](./gdwps-canada.md) — ~39 km computational vs GDWPS's ¼° 3-grid mosaic, 3-hourly throughout vs GDWPS's hourly-to-84h, but a longer 16-day horizon vs GDWPS's 10 days.
- **Member packaging:** One GRIB2 file per variable per time step, containing **all 21 members as separate GRIB messages** — there is no member token in the filename. Live-verified encoding: GRIB2 Product Definition Template 4.1, `numberOfForecastsInEnsemble = 21`, `perturbationNumber` 0–20 (0 = control, 1–20 = perturbed), `centre = cwao`. A single `HTSGW` time-step file is ~15.6 MB.
- **Derived products distributed:** None as raw GRIB — the Datamart ships the raw members only. Ensemble mean, spread, and probability/percentile products are not published as GRIB files; they are available through ECCC's GeoMet-Weather web services or must be computed by the user from the members.

---

## Data assimilation
- **Assimilates wave observations:** No. GEWPS has no wave data assimilation system.
- **Initialization:** Member-continuous cycling rather than a pseudo-analysis (a difference from GDWPS). Each member writes a restart file at the 12-hour mark, and the next run of that same member restarts from it — e.g. the 00 UTC forecast starts from the previous day's 12 UTC forecast at 12-hour lead time.

---

## What it provides
Probabilistic global wave forecasts (surface, GRIB2, all 21 members per file). Live-verified field list on the 2026-07-24 00 UTC run (16 wave fields; note GEWPS does **not** distribute ice concentration, unlike GDWPS):

- **Combined sea state:** significant height of combined wind waves and swell (`HTSGW`)
- **Wind-sea partition:** significant height (`WVHGT`), direction (`WVDIR`), peak period (`PPERWW`)
- **Swell partitions (first and second):** significant height (`SWHFSWEL`, `SWHSSWEL`), mean direction (`MWDFSWEL`, `MWDSSWEL`), peak period (`PWPFSWEL`, `PWPSSWEL`)
- **Total-spectrum period / direction:** peak wave period (`PWPER`), mean zero-crossing period (`MZWPER`), mean wave direction (`WWSDIR`), peak wave direction (`PWAVEDIR`)
- **Surface Stokes drift:** u- and v-components (`USSD`, `VSSD`)

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTP)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (21 ensemble members per file)
- **Official download location:**
  https://dd.weather.gc.ca/today/model_gewps/25km/
  - **Path template:** `https://dd.weather.gc.ca/today/model_gewps/25km/{HH}/` — `{HH}` = run (`00`/`12`)
  - **Filename convention:** `{YYYYMMDD}T{HH}Z_MSC_GEWPS_{VAR}_Sfc_LatLon0.25_PT{hhh}H.grib2` (e.g. `20260724T00Z_MSC_GEWPS_HTSGW_Sfc_LatLon0.25_PT015H.grib2`) — members are inside the file, not in the name
  - Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_gewps/readme_gewps-datamart_en/

---

## Notes
- **Distribution grid ≠ model grid.** The public product is a single interpolated global 0.25° lat-lon grid; the model computes on Yin–Yang overlapping grids at ~39 km. The `25km` in the datamart path refers to the distributed grid spacing, not the computational resolution.
- **Raw members, not probabilities.** Users wanting exceedance probabilities, percentiles, or ensemble mean/spread must derive them from the 21 members (or use GeoMet-Weather). This is the same phenomenon-over-ensemble-status packaging used elsewhere in the repository for marine ensembles.
- **Model physics:** input/dissipation source terms ST4 (Ardhuin et al. 2010); DIA nonlinear interactions; JONSWAP bottom friction; Battjes–Janssen depth-induced breaking; third-order QUICKEST propagation with ULTIMATE TVD limiter. Bathymetry from ETOPO1; coastline from GSHHS. Overall time step 900 s. The physics package matches GDWPS; the main differences are the ensemble forcing, the coarser Yin–Yang computational grid, and the longer horizon.
- **Relationship to other ECCC systems:**
  - Deterministic sibling: [GDWPS](./gdwps-canada.md) — same WW3 physics, higher resolution, shorter (10-day) horizon.
  - Atmospheric driver: [GEPS](../../../ensemble_models/global/canada/geps.md) 8.0.0 (per-member 10 m winds and ice).
  - Regional ensemble counterpart: **REWPS** (Regional Ensemble Wave Prediction System) — the regional-waters ensemble sibling. *(Entry pending.)*

---

## Recent version history

Versions are implemented at the stated date's 12 UTC run.

### GEWPS 1.4.0 — operational 14 April 2026 (current)
Adaptation to ECCC's new High Performance Computing infrastructure. Infrastructure-only; no scientific or configuration changes. The v1.3.0 technical specification still describes the current scientific configuration.

### GEWPS 1.3.0 — operational 11 June 2024
Wind and ice forcing moved to GEPS version 8.0.0.

### GEWPS 1.2.0 — operational 28 June 2022
HPC-infrastructure adaptation.

### GEWPS 1.1.0 — operational 1 December 2021
Status changed from experimental to operational. Adopted GEPS v7.0.0 forcing and WAVEWATCH III v7 (with the negative-peak-period bugfix); hourly wind input extended past 168 h.

### GEWPS 1.0.0 — experimental 17 November 2020
Initial experimental implementation at CMC.

---

## Official documentation
- GEWPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_gewps/readme_gewps_en/
- GEWPS Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_gewps/readme_gewps-datamart_en/
- Technical specifications (current, describes v1.3.0 config): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GEWPS_e.pdf
- Technical specifications (v1.3.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_specifications/tech_specifications_GEWPS_1.3.0_e.pdf
- Technical note (v1.3.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_gewps-130_e.pdf
- Factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_gewps_e.pdf
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_GEWPS_en.svg
- GEWPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_gewps/changelog_gewps_en/
- Open Government Portal metadata: https://open.canada.ca/data/en/dataset/214499e5-99c6-401f-9d7e-c16611680719
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/
