# ICON-ART Pollen (DWD Daily Mean Pollen Concentration for Germany)

## What this model is
DWD's operational **pollen dispersion forecast**, produced with ICON-ART in limited-area mode over Europe and distributed publicly as a **Germany-domain subset**. It forecasts airborne concentrations of five allergenic pollen types — hazel, alder, birch, grasses, and ragweed — as daily mean concentrations in grains per cubic metre, out to +5 days.

The system was developed jointly by the German, Austrian, and Swiss national weather services (D-A-CH) together with the Karlsruhe Institute of Technology (KIT), building on the EMPOL pollen-emission parameterization. Plant flowering readiness is computed at each grid point from a temperature-sum model, which then drives emission, transport, and deposition within the online-coupled ART framework. The intended use is public allergy/health information; DWD explicitly states the forecasts are **not suitable for clinical studies**.

> **Note on naming:** This is a *different configuration and a different distribution channel* from [ICON-ART-EU](./icon-art-eu.md), DWD's ICON-EU-nested **mineral-dust** forecast published as GRIB2 under `/weather/nwp/v1/m/`. Both are ICON+ART, but they differ in domain, cycle, format, output cadence, and operating unit. See *Notes*.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD) — Zentrum für Medizin-Meteorologische Forschung Freiburg (ZMMF / Center for Human Biometeorological Research); ART module developed by the Karlsruhe Institute of Technology (KIT)
- **Country / region:** Germany (model domain: Europe; distributed domain: Germany)
- **Development partnership:** D-A-CH (DWD, GeoSphere Austria, MeteoSwiss) + KIT
- **Operational since:** September 2021 (DWD operational pollen forecast system for Central Europe and the Mediterranean); publicly announced April 2022
- **Contact:** mm.freiburg@dwd.de

---

## What area it covers
- **Coverage (distributed):** Germany — **47.2°–56.2°N, 5.6°–15.1°E**
- **Coverage (model run):** Europe (ICON-ART limited-area mode), so long-range transport into Germany is represented — e.g. birch pollen advected from Scandinavia, ragweed from Hungary, Serbia, the Rhône valley, or the Po plain
- **Domain details:** Distributed on a **regular lat/lon grid, 153 × 145 points at 0.0625°** (verified from file). Native model grid is R3B08 (~6.5 km); 0.0625° is DWD's standard ICON-EU output grid spacing (~7 km at these latitudes in the meridional direction, ~4.3 km zonal at 51.7°N).

---

## Basic details
- **Model type:** Regional atmospheric composition — bioaerosol (pollen) dispersion
- **Model system / core:** ICON-ART in limited-area mode; pollen emission via **EMPOL** (Zink et al. 2013), with a temperature-sum phenology model for flowering readiness
- **Horizontal resolution:** ~6.5 km native (R3B08); distributed at 0.0625° regular lat/lon
- **Vertical levels:** N/A for the distributed product — output is a single near-surface concentration field per species (2D). Model-internal level count not documented in the open dataset description.
- **Forecast length:** **+150 h computed**; distributed as **6 daily means** (run day through +5 days)
- **Update frequency / cycles:** **1× daily**, run at ~03:35 UTC (files observed on the server ~05:20 UTC)
- **Temporal output resolution:** **Daily means** (24 h, `cell_methods = "time: mean"`; `time_bnds` span 00:00–23:00 UTC of each calendar day). No sub-daily output is distributed.

---

## Meteorological driver
- **Driving NWP model:** Self — ICON-ART; meteorology and pollen evolve within a single ICON run
- **Coupling:** Online (ART is integrated in ICON, sharing the dynamical core, grid, and transport operators)
- **Companion meteorology:** No meteorological fields are bundled in the pollen files. DWD directs users to the **ICON-EU 00 UTC run** for matching meteorology: https://opendata.dwd.de/weather/nwp/icon-eu/grib/00/

---

## Chemistry and aerosols
- **Gas-phase chemical mechanism:** None — this configuration carries no gas-phase chemistry
- **Aerosol treatment:** Pollen carried as tracer species; one prognostic field per pollen type
- **Aerosol components represented:** Pollen grains only (five taxa; see *What it provides*). No dust, sulfate, nitrate, carbonaceous, or sea-salt species in this product.
- **Processes:** Emission (EMPOL, gated by modelled flowering readiness), advective and turbulent transport, sedimentation, dry and wet deposition

---

## Emissions
- **Pollen (biogenic):** Online, computed by ART via the EMPOL parameterization. Emission is conditioned on a per-gridpoint temperature-sum phenology model determining plant flowering readiness, combined with species distribution/pollen-source maps.
- **Anthropogenic / wildfire / dust / sea salt:** Not applicable — this configuration forecasts pollen only.

---

## Data assimilation
- **Assimilates composition observations:** No pollen-observation assimilation is documented for the distributed product. Meteorological initial conditions carry ICON's own assimilation. (DWD's separate statistical *Pollenflug-Gefahrenindex* does ingest pollen-trap and phenological observations — see *Notes*.)

---

## What it provides
Daily mean near-surface pollen concentration in **grains m⁻³** (`1/m^3`), one NetCDF variable and one file per species. Availability is **seasonal per taxon**:

| Common name | Latin name | NetCDF variable | Season (day of year) | Season dates |
|---|---|---|---|---|
| Hazel | *Corylus* | `CORY` | 1 – 146 | Jan 1 – May 26 |
| Alder | *Alnus* | `ALNU` | 1 – 146 | Jan 1 – May 26 |
| Birch | *Betula* | `BETU` | 30 – 161 | Jan 30 – Jun 10 |
| Grasses | *Poaceae* | `POAC` | 60 – 305 | Mar 1 – Nov 1 |
| Ragweed | *Ambrosia* | `AMB` | 213 – 280 | Aug 1 – Oct 7 |

Files exist **only during the respective season**, so the number of files present on any given day ranges from 1 to 4 (maximum overlap is Mar 1 – May 26: hazel, alder, birch, grasses).

No derived index, no health category, and no meteorological fields are included in this product.

---

## Data availability
- **Is the data free?** Yes
- **License:** CC BY 4.0 (DWD Open Data / GeoNutzV; attribution required) — https://www.dwd.de/copyright
  - **Suggested citation (per DWD):** *DWD, daily mean of pollen concentration based on ICON-ART, last accessed: \<date\>.*
- **Is the data downloadable?** Yes — plain HTTPS directory, no registration
- **Data format:** NetCDF (classic, NETCDF3_64BIT_OFFSET), CF-1.6 conventions
- **Official download location:**
  https://opendata.dwd.de/climate_environment/health/forecasts/pollen/
  - **Filename convention:** `icon-art-pollen_germany_regular_lon_lat_{VAR}_{YYYYMMDDHH}.nc`
    - `{VAR}` — species variable (`CORY`, `ALNU`, `BETU`, `POAC`, `AMB`)
    - `{YYYYMMDDHH}` — run date and hour (always `00`)
  - **Example:** `icon-art-pollen_germany_regular_lon_lat_POAC_2026072100.nc`
- **File size:** ~535 KB per species per run (6 × 145 × 153 float32 + coordinates)
- **Retention:** Short rolling window — **~3 days** of runs present at check time. No archive is offered on the open-data server; users needing history must self-archive.
- **Dataset description PDFs (in the same directory):**
  - German: `BESCHREIBUNG_ICON_ART_pollen_concentration_daily_de.pdf` (rev. 2025-02-18)
  - English: `DESCRIPTION_ICON_ART_pollen_concentration_daily_en.pdf` (rev. 2024-01-05)

---

## Notes
- **Verified 2026-07:** Header, grid, time axis, and value ranges confirmed by direct inspection of a live file (`POAC`, 2026-07-21 run). Grid is exactly 153 × 153-uniform-spacing lat/lon as documented; 6 daily-mean steps with `time_bnds` covering full UTC calendar days; values 0–98.5 grains m⁻³ with no missing data.
- **Distinct from [ICON-ART-EU](./icon-art-eu.md) (dust):** shared ART lineage, but a separate configuration and a separate distribution channel. The dust feed is GRIB2 on the native unstructured grid under `/weather/nwp/v1/m/`, 4× daily, hourly-to-3-hourly, Europe-wide, continuous. The pollen product is NetCDF on a regular lat/lon grid under `/climate_environment/health/`, 1× daily, daily means, Germany-only, seasonal. Keep both entries.
- **Resolution wording:** DWD's public pollen page cites "7 km"; the dataset description cites "~6.5 km (R3B08)". These refer to the ICON-EU-class output grid (0.0625°) and the native triangular grid respectively — not a contradiction.
- **Not the same as the *Pollenflug-Gefahrenindex*:** DWD separately publishes a statistical/expert pollen index at `https://opendata.dwd.de/climate_environment/health/alerts/s31fg.json` — an ordinal 0–3 loading index for **8 taxa** (hazel, alder, ash, birch, grasses, rye, mugwort, ragweed) across **27 regions**, updated daily at 11:00 local. That product is region-coded JSON, not gridded raster, and is built from pollen-trap measurements, phenological observations, and MOS statistics — with ICON-ART as one supporting input. It falls outside the raw-gridded-data scope; noted here for disambiguation only.
- **Research status caveat:** DWD states these forecasts are subject to ongoing research and development and are explicitly not suitable for clinical studies.
- **Related national systems:** MeteoSwiss and GeoSphere Austria run sibling ICON-ART pollen configurations from the same D-A-CH/KIT development effort. Their open-data status is not covered by this entry.
- **Flag — unverified variable names:** `POAC` and `BETU` are confirmed from live/cached directory listings. `CORY`, `ALNU`, and `AMB` follow the abbreviation convention indicated in DWD's dataset description but could not be confirmed against live files (off-season at verification time). **`AMB` in particular is uncertain** — the source PDF's bolding suggests a 3-letter form where the other four are 4-letter, so `AMBR` is a plausible alternative. Re-verify after 1 August (ragweed season opens) and in January (hazel/alder).

---

## Official documentation
- DWD Open Data, pollen forecasts: https://opendata.dwd.de/climate_environment/health/forecasts/pollen/
- Dataset description (EN): https://opendata.dwd.de/climate_environment/health/forecasts/pollen/DESCRIPTION_ICON_ART_pollen_concentration_daily_en.pdf
- Dataset description (DE): https://opendata.dwd.de/climate_environment/health/forecasts/pollen/BESCHREIBUNG_ICON_ART_pollen_concentration_daily_de.pdf
- DWD pollen research overview: https://www.dwd.de/DE/leistungen/pollen/pollenforschung.html
- DWD press release, ICON-ART pollen forecasting operational (April 2022): https://www.dwd.de/DE/presse/pressemitteilungen/DE/2022/20220405_pollenprognose_mit_icon-art_news.html
- KIT ICON-ART: https://www.icon-art.kit.edu/
- ICON-ART module documentation: https://docs.icon-model.org/atmosphere/art/art.html

### Key references
- Vogel, H., Pauling, A., Vogel, B. (2008). Numerical simulation of birch pollen dispersion with an operational weather forecast system. *Int. J. Biometeorol.*, 52, 805–814. https://doi.org/10.1007/s00484-008-0174-3
- Zink, K., Pauling, A., Rotach, M. W., Vogel, H., Kaufmann, P., Clot, B. (2013). EMPOL 1.0: a new parameterization of pollen emission in numerical weather prediction models. *Geosci. Model Dev.*, 6, 1961–1975. https://doi.org/10.5194/gmd-6-1961-2013
- Rieger, D., et al. (2015). ICON–ART 1.0 — a new online-coupled model system from the global to regional scale. *Geosci. Model Dev.*, 8, 1659–1676. https://doi.org/10.5194/gmd-8-1659-2015
