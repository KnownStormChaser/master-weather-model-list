# GDWPS (Global Deterministic Wave Prediction System)

## What this model is
The Global Deterministic Wave Prediction System (GDWPS) is Canada's operational global wave forecasting system, producing deterministic forecasts of ocean surface waves worldwide out to 240 hours.

GDWPS is built on the WAVEWATCH III® spectral wave model, forced by 10 m winds and sea-ice concentration from the [Global Deterministic Prediction System (GDPS)](../../../nwp_models/global/canada/gem-global.md). It provides the primary source of global wave guidance for Canadian marine forecasting and supplies boundary conditions for Canada's regional wave system, [RDWPS](../../regional/canada/rdwps-canada.md).

The current operational version is **GDWPS 1.11.0**, implemented at the 12 UTC run on **26 May 2026**.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Canadian Centre for Meteorological and Environmental Prediction (CCMEP), Environment and Climate Change Canada (ECCC)
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global oceans
- **Distributed grid:** Single global regular latitude–longitude grid, **1440 × 721** points, **0.25°**, first grid point 90°S 0°E (spans 90°S→90°N, 0°→359.75°E). Live-verified on the 2026-07-24 00 UTC run (`centre: cwao`).
- **Computational domain:** Three overlapping WW3 grids at ¼° — **p01: 59°N–86°N**, **p02: 65°S–65°N**, **p03: 80°S–62°S** — mosaicked and interpolated onto the single distributed grid. The two polar grids overlap the mid-latitude grid at 59–65°N and 62–65°S; the model does not compute above 86°N or below 80°S.

---

## Basic details
- **Model type:** Deterministic global wave model
- **Grid system:** Multi-grid WW3 mosaic (3 overlapping grids) on the computational side; single global 0.25° regular lat-lon grid on distribution
- **Core wave model:** WAVEWATCH III® (WW3) version 7.0
- **Spectral resolution:** 36 directional bins (10° each) × 36 frequency bins from 0.035 Hz to 0.984 Hz (increment factor 1.10), with a parametric tail fitted at higher frequencies
- **Horizontal resolution:** 0.25° (¼°)
- **Forecast length:** 240 hours (10 days)
- **Update frequency / cycles:**
  - Forecast runs: 2× daily (00, 12 UTC) — the publicly distributed product
  - Pseudo-analysis: 4× daily (6-hour windows centred at 00, 06, 12, 18 UTC), used only to initialize the forecasts
- **Temporal output resolution:** Hourly from 000–084 h, then 3-hourly from 084–240 h (137 time steps; live-verified). *Note: the datamart nomenclature page still states hourly-to-048h then 3-hourly, which is stale — the live product is hourly to 084h.*

---

## Forcing and nesting
- **Wind forcing:** GDPS 10 m winds (`UU`, `VV`), version 10.0.0
  - Pseudo-analysis: GDPS **G2** (delayed-cutoff) run, hourly
  - Forecast: GDPS **G1** run, hourly
- **Ice forcing:** GDPS sea-ice concentration (`GL`), version 10.0.0 — hourly in the pseudo-analysis (G2), 3-hourly in the forecast (G1). Wave growth is attenuated where ice concentration is 25–75% and suppressed above 75%.
- **Current forcing:** None (not coupled to an ocean-current model)
- **Parent for:** [RDWPS](../../regional/canada/rdwps-canada.md) — GDWPS supplies lateral wave boundary conditions to RDWPS's ocean domains (Northeastern Pacific, Northwestern Atlantic).

---

## Data assimilation
- **Assimilates wave observations:** No. GDWPS has no wave data assimilation system.
- **Initialization:** A continuously cycled pseudo-analysis. The model is run with delayed-cutoff GDPS G2 winds and ice in 6-hour windows centred on 00, 06, 12, 18 UTC, each restarting from the previous window's restart file. The 00 UTC forecast starts from the previous day's 18 UTC pseudo-analysis; the 12 UTC forecast starts from the 06 UTC pseudo-analysis, with GDPS G1 IAU data bridging the 3-hour gap to G1 availability.

---

## What it provides
Deterministic global wave forecasts (surface, GRIB2, one file per variable per time step). Live-verified field list on the 2026-07-24 00 UTC run:

- **Combined sea state:** significant height of combined wind waves and swell (`HTSGW`)
- **Wind-sea partition:** significant height (`WVHGT`), direction (`WVDIR`), peak period (`PPERWW`)
- **Swell partitions (first and second):** significant height (`SWHFSWEL`, `SWHSSWEL`), mean direction (`MWDFSWEL`, `MWDSSWEL`), peak period (`PWPFSWEL`, `PWPSSWEL`)
- **Total-spectrum period / direction:** peak wave period (`PWPER`), mean zero-crossing period (`MZWPER`), mean wave direction (`WWSDIR`), peak wave direction (`PWAVEDIR`)
- **Surface Stokes drift:** u- and v-components (`USSD`, `VSSD`)
- **Sea ice:** ice concentration (`ICEC`)

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTP)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://dd.weather.gc.ca/today/model_gdwps/25km/
  - **Path template:** `https://dd.weather.gc.ca/today/model_gdwps/25km/{HH}/` — `{HH}` = run (`00`/`12`)
  - **Filename convention:** `{YYYYMMDD}T{HH}Z_MSC_GDWPS_{VAR}_Sfc_LatLon0.25_PT{hhh}H.grib2` (e.g. `20260724T00Z_MSC_GDWPS_HTSGW_Sfc_LatLon0.25_PT051H.grib2`)
  - Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_gdwps/readme_gdwps-datamart_en/

---

## Notes
- **Distribution grid ≠ model grid.** The public product is a single interpolated global 0.25° lat-lon grid. Users wanting the native 3-grid WW3 mosaic will not find it distributed; only the mosaicked, regridded output is public.
- **Model physics:** input/dissipation source terms ST4 (Ardhuin et al. 2010); DIA nonlinear interactions; JONSWAP bottom friction; Battjes–Janssen depth-induced breaking; third-order QUICKEST propagation with ULTIMATE TVD limiter. Bathymetry from ETOPO1; coastline from GSHHS. Overall time step 450 s.
- **Relationship to other ECCC systems:**
  - Meteorological driver: [GDPS](../../../nwp_models/global/canada/gem-global.md) 10.0.0 (10 m winds, ice concentration). Note that GDPS 10.0.0 introduced AI spectral nudging toward GEML, so GDWPS 1.11.0 is indirectly driven by a partly data-driven atmospheric forecast.
  - Regional deterministic sibling: [RDWPS](../../regional/canada/rdwps-canada.md), which receives GDWPS boundary conditions.
  - Ensemble counterpart: [GEWPS](./gewps-canada.md) (Global Ensemble Wave Prediction System) — the ensemble sibling of this system, driven by the GEPS atmospheric ensemble.
  - Developed in partnership with NCEP/NOAA (GDWPS began as an experimental system in June 2015; declared operational November 2017).

---

## Recent version history

Versions are implemented at the stated date's 12 UTC run.

### GDWPS 1.11.0 — operational 26 May 2026 (current)
Single change: winds and ice moved to **GDPS version 10.0.0**.

### GDWPS 1.10.0 — operational 14 April 2026
Adaptation to ECCC's new High Performance Computing infrastructure. Infrastructure-only; no scientific or configuration changes.

### GDWPS 1.9.0 — operational 11 June 2024
Upgraded driving atmospheric model to GDPS version 9.0.0.

### GDWPS 1.8.0 — operational 28 June 2022
Adaptation to the (then) new HPC infrastructure.

### GDWPS 1.7.0 — operational 1 December 2021
GDPS v8.0.0 forcing with re-optimized parameterization; adoption of WAVEWATCH III v7 (same physics as v5.16, plus a negative-peak-period bugfix); activation of a Miche-style limiter for shallow-water wave breaking; hourly wind input extended past 144 h.

### GDWPS 1.6.0 — operational 21 January 2020
HPC-infrastructure adaptation.

### GDWPS 1.3.0 — declared operational 1 November 2017
Declared operational after running experimentally since 2015.

### Experimental — 23 June 2015
Initial experimental implementation, developed in partnership with NCEP/NOAA.

---

## Official documentation
- GDWPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_gdwps/readme_gdwps_en/
- GDWPS Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_gdwps/readme_gdwps-datamart_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GDWPS_e.pdf
- Technical specifications (v1.11.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_specifications/tech_specifications_GDWPS_1.11.0_e.pdf
- Technical note (v1.9.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_gdwps-190_e.pdf
- Factsheet: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/fact_sheets/factsheet_gdwps_e.pdf
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_GDWPS_en.svg
- GDWPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_gdwps/changelog_gdwps_en/
- Open Government Portal metadata: https://open.canada.ca/data/en/dataset/803a6e2a-41ed-44c2-9eeb-1b5306b4048e
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key reference
- Bernier, N. B., and Coauthors, 2016: Operational wave prediction system at Environment Canada: Going global to improve regional forecast skill. *Weather and Forecasting*, 31, 353–370. https://doi.org/10.1175/WAF-D-15-0087.1
