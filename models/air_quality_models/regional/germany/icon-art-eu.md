# ICON-ART-EU (European Mineral Dust Forecast)

## What this model is
ICON-ART-EU is DWD's regional **mineral-dust** forecast for Europe — the European nest of ICON (ICON-EU) extended with the ART (Aerosols and Reactive Trace gases) module, run online-coupled. It predicts the emission, transport, sedimentation, and dry/wet deposition of mineral dust (notably Saharan dust transported into Europe), and distributes the standard ICON-EU meteorological fields alongside a set of ART dust parameters.

The forecasts became available on the DWD Open Data Server in **April 2025**, under the `/weather/nwp/v1/m/` tier that also hosts the global ICON-ART and the ensemble counterparts. The "online-coupled" design means dust and meteorology evolve together within a single model run, with dust–radiation and dust–cloud (ice-nucleation) feedbacks acting on the forecast.

> **Note on scope / naming:** "ICON-ART-EU" is sometimes used loosely for DWD's ICON-ART-based *pollen* forecast as well. That is a **separate configuration on a separate distribution channel** — it *is* openly distributed, but as NetCDF under `/climate_environment/health/forecasts/pollen/`, not as GRIB in this feed. The open `icon-art-eu` feed contains dust and standard meteorology only, with no pollen fields. See [ICON-ART Pollen](./icon-art-pollen.md) for the pollen product; this entry covers the open-data mineral-dust product.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service; GRIB centre `edzw`, Offenbach), with the ART module developed and maintained by the Karlsruhe Institute of Technology (KIT)
- **Country:** Germany
- **On open data since:** April 2025

---

## What area it covers
- **Coverage:** Europe (the ICON-EU nest domain)
- **Domain details:** Native ICON-EU icosahedral (triangular) **unstructured grid**; the GRIB2 messages carry no lat/lon, so georeferencing requires the ICON-EU grid/coordinate description file (as with all native-grid ICON output).

---

## Basic details
- **Model type:** Regional atmospheric composition / mineral-dust forecast (online-coupled)
- **Model system / core:** ICON-EU nest + ART module (online-coupled meteorology–aerosol), same dynamical core, grid, and physics as [ICON-EU](../../../nwp_models/regional/germany/icon-eu.md)
- **Horizontal resolution:** ~6.5 km (native ICON-EU nest, unstructured grid)
- **Vertical levels:** ICON-EU model levels (3D dust fields such as `DUST_TOTAL_MC` are on model levels; see the [ICON-EU](../../../nwp_models/regional/germany/icon-eu.md) entry for the level count)
- **Forecast length:** 120 hours (5 days)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly to +72 h, then 3-hourly to +120 h (verified from the feed)

---

## Meteorological driver
- **Driving NWP model:** Self — ICON-ART-EU *is* ICON-EU with ART; ICON-EU is two-way coupled within global ICON.
- **Coupling:** Online (two-way) — dust tracers use the same advection and subgrid transport operators as ICON's moisture fields; dust–radiation and dust–cloud feedbacks act on the meteorological forecast.

---

## Chemistry and aerosols
- **Composition species:** Mineral dust only (no gas-phase chemistry, and no other aerosol species, in this operational configuration).
- **Dust representation:** Three dust modes / size classes (A, B, C), each carried as mass-concentration fields on model levels, with companion `*0` fields.
- **ART processes (online):** Dust emission, advective/convective/turbulent transport, gravitational sedimentation, and dry and wet (convective + grid-scale) deposition.
- **Feedbacks:** Aerosol–radiation (dust attenuates shortwave radiation) and aerosol–cloud (dust as ice-nucleating particles).

---

## Emissions
- **Dust (natural):** Online mineral-dust emission computed by ART, driven by the model's own near-surface meteorology and surface characteristics.
- **Anthropogenic / biogenic / other:** Not applicable — this configuration forecasts natural mineral dust only.

---

## Data assimilation
- **Assimilates composition observations:** No dust observation assimilation is documented; initial conditions are inherited from the ICON/ICON-EU analysis. Meteorology carries ICON-EU's own assimilation.

---

## What it provides
The feed distributes the **standard ICON-EU meteorological parameter set** (2 m temperature, cloud cover, wind, precipitation, pressure, etc. — ~100+ fields) **plus 28 ART mineral-dust parameters**, including:

- **`TAOD_DUST`** — Total-atmosphere optical depth due to mineral dust aerosol (dimensionless). *This is the "dust optical depth" downstream sites typically use.*
- **`DUST_TOTAL_MC`** — Total dust mass concentration on model levels (3D; kg m⁻³)
- **`DUST_TOTAL_MC_VI`** — Vertically integrated (column) total dust mass (kg m⁻²)
- **`DUST_MAX_TOTAL_MC_LAYER`** — Maximum layer total dust mass concentration
- **`DUSTA` / `DUSTB` / `DUSTC`** (+ `DUSTA0` / `DUSTB0` / `DUSTC0`) — per-mode dust fields (modes A/B/C)
- **`AER_DUST`, `CEIL_BSC_DUST`, `SAT_BSC_DUST`** — dust aerosol and ceilometer/satellite backscatter diagnostics
- **`ACCEMISS_DUST{A,B,C}`** — accumulated dust emission per mode
- **`ACCDRYDEPO_DUST{A,B,C}`** — accumulated dry deposition per mode
- **`ACCSEDIM_DUST{A,B,C}`** — accumulated sedimentation per mode
- **`ACCWETDEPO_CON_DUST{A,B,C}` / `ACCWETDEPO_GSP_DUST{A,B,C}`** — accumulated wet deposition, convective and grid-scale, per mode

No pollen and no other pollutant species are provided.

---

## Data availability
- **Is the data free?** Yes
- **Licence:** DWD Open Data licence (GeoNutzV; CC BY 4.0-compatible, attribution required)
- **Is the data downloadable?** Yes
- **Data format:** GRIB2 (native ICON-EU unstructured grid)
- **Primary access:** DWD Open Data Server
  - Root: https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eu/
  - **Path structure:** `.../icon-art-eu/p/{VARIABLE}/r/{YYYY-MM-DDTHH:MM}/s/PT{hhh}H{mm}M.grib2`
    - `{VARIABLE}` — parameter folder (e.g. `TAOD_DUST`, `DUST_TOTAL_MC_VI`, `T_2M`)
    - `r/{run}` — run initialization time, ISO-8601 (`00/06/12/18` UTC)
    - `s/PT{hhh}H{mm}M.grib2` — forecast lead time (hourly to +72, 3-hourly to +120)
- **Ensemble companion:** `icon-art-eu-eps` (same tier).
- **Retention note:** Short rolling retention (~24 h); elapsed lead-times are pruned as a run ages, so at any given moment only the still-forecast lead-times of the most recent runs are present.
- **Georeferencing note:** GRIB messages are on the native ICON unstructured grid with no embedded lat/lon — pair with the ICON-EU grid/coordinate description to georeference.

---

## Notes
- **Identity correction (verified 2026-07):** The open `icon-art-eu` feed is DWD's **mineral-dust** forecast (launched April 2025), *not* a pollen forecast. All 132 variable folders were enumerated: standard ICON-EU meteorology + 28 dust ART parameters, zero pollen fields.
- **Pollen product (separate entry, verified 2026-07):** DWD runs ICON-ART in limited-area mode for a European pollen forecast — hazel, alder, birch, grasses, ragweed — operational since September 2021 and co-developed with GeoSphere Austria, MeteoSwiss, and KIT via the EMPOL scheme (Zink et al. 2013). The Germany-domain subset **is** openly distributed, as daily-mean NetCDF at `https://opendata.dwd.de/climate_environment/health/forecasts/pollen/` — a different path tree, format, cadence, and domain from this dust feed. It is catalogued separately as [ICON-ART Pollen](./icon-art-pollen.md).
- **Not "dust only" in file terms:** the feed carries the full standard ICON-EU meteorological field set in addition to the dust parameters; dust is simply the distinguishing atmospheric-composition capability.
- **Companion:** the global [ICON-ART](../../global/germany/icon-art.md) mineral-dust forecast is the global counterpart; ICON-ART-EU is the regional (ICON-EU nest) dust configuration. The third DWD ART product is [ICON-ART Pollen](./icon-art-pollen.md), a distinct limited-area pollen configuration on its own open-data channel.

---

## Recent version history

### April 2025 — ICON-ART on DWD Open Data
DWD published the ICON-ART NWP system (global ICON-ART and regional ICON-ART-EU, plus ensemble counterparts) on the Open Data Server under `/weather/nwp/v1/m/`, adding the ART mineral-dust parameters (`TAOD_DUST`, `DUST_TOTAL_MC`/`_VI`, and per-mode emission/deposition/sedimentation accumulations) to the standard parameter set.

---

## Official documentation
- DWD Open Data announcement (ICON-ART, April 2025): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_april2025_1.html
- DWD Open Data Server, ICON-ART-EU: https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eu/
- DWD ICON-ART / COSMO-ART overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/03_environmental_forecasts/icon_art_cosmo_art_en.html
- ICON model / ART parameter documentation: https://docs.icon-model.org/atmosphere/art/art.html
- KIT ICON-ART: https://www.icon-art.kit.edu/

### Key references
- Rieger, D., et al. (2015). ICON-ART 1.0 — a new online-coupled model system from the global to regional scale. *Geosci. Model Dev.*, 8, 1659–1676.
- Hoshyaripour, G. A., et al. (2026). The atmospheric composition component of the ICON modeling framework: ICON-ART version 2025.10. *Geosci. Model Dev.*, 19, 1645–1681.
