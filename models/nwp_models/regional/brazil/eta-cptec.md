# ETA (CPTEC/INPE Regional Model)

## What this model is
The Eta model is a regional numerical weather prediction (NWP) system operated by CPTEC/INPE, built on the Eta step-mountain vertical coordinate. It provides short- to medium-range deterministic forecasts over South America and selected sub-national domains, and is run in four publicly distributed configurations that differ by domain, horizontal resolution, forecast length, and file format.

---

## Who runs it
- **Organization:** CPTEC/INPE (Center for Weather Forecasting and Climate Studies / National Institute for Space Research)
- **Country / region:** Brazil

---

## What area it covers
- **Coverage:** South America (continental domains) plus a São Paulo–Rio de Janeiro local domain
- **Domain details:** All configurations are distributed as regular lat/lon grids (verified from the GrADS `.ctl` descriptors):
  - `ams_08km`: 875 × 931 @ 0.08° (~8.9 km) — 90.00°W–20.08°W, 55.00°S–19.40°N
  - `ams_40km`: 144 × 157 @ 0.40° (~44 km) — 83.00°W–25.80°W, 50.20°S–12.20°N
  - `ons_40km`: **identical grid to `ams_40km`** (144 × 157 @ 0.40°, same bounds) — same domain, run separately for the ONS energy sector
  - `rjsp_01km`: 450 × 280 @ 0.01° (~1.1 km) — 46.30°W–41.81°W, 24.40°S–21.61°S (RJ/SP corridor)

---

## Model configurations

| Config | Domain | Horizontal res. | Forecast length | Output step | Cycle | Format | Filename pattern |
|---|---|---|---|---|---|---|---|
| `ams_08km` | South America | ~8 km | +264 h (11 days) | Hourly | 00 UTC, 1×/day | **GRIB2** | `Eta_ams_08km_<init>_<valid>.grib2` |
| `ams_40km` | South America | ~40 km | +264 h (11 days) | Hourly | 00 UTC, 1×/day | GRIB1 (`.grb`) | `eta_40km_<init>+<valid>.grb` |
| `ons_40km` | ONS energy-sector domain | ~40 km | +264 h (11 days) | Hourly | 00 UTC, 1×/day | GRIB1 (`.grb`) | `eta_40km_<init>+<valid>.grb` |
| `rjsp_01km` | Rio de Janeiro / São Paulo | ~1 km | +72 h (3 days) | Hourly | 00 UTC, 1×/day | GRIB1 (`.grb`) | `eta_01km_<init>+<valid>.grb` |

(`<init>` and `<valid>` are `YYYYMMDDHH`. Note that `ams_40km` and `ons_40km` use the **same** `eta_40km_` filename prefix and are distinguished only by directory path.)

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** Eta (eta step-mountain vertical coordinate)
- **Dynamical formulation:** TBD (classic Eta is hydrostatic; not confirmed for the current CPTEC configuration)
- **Convection-allowing:** Only `rjsp_01km` (~1 km) is convection-allowing; the 8 km and 40 km configurations use parameterized convection
- **Horizontal resolution:** Per configuration — ~8 km / ~40 km / ~40 km / ~1 km (see table)
- **Vertical levels:** All four configurations distribute **22 pressure levels**: 1020, 1000, 950, 925, 900, 850, 800, 750, 700, 650, 600, 550, 500, 450, 400, 350, 300, 250, 200, 150, 100, 50 hPa (verified from the `.ctl` `zdef`). The native Eta step-mountain level count is not exposed in the output — unconfirmed.
- **Model top:** Highest distributed pressure level is 50 hPa; the native model top is higher but not determinable from the output — unconfirmed.
- **Forecast length:** +264 h (11 days) for `ams_08km`, `ams_40km`, `ons_40km`; +72 h (3 days) for `rjsp_01km`
- **Update frequency / cycles:** 1× daily (00 UTC) — all four configurations
- **Temporal output resolution:** Hourly — all four configurations

---

## Data assimilation (optional)
- **Data assimilation:** TBD

---

## Initial and boundary conditions
- **Initial / boundary conditions:** TBD — CPTEC's regional Eta is typically nested in a global driver (plausibly CPTEC's BAM), but this is unconfirmed for the public feed.

---

## What it provides
Deterministic regional forecasts on 22 pressure levels plus surface/near-surface fields. The two formats use different naming conventions: `ams_08km` (GRIB2) uses NCEP/UPP-style names (46 variables); the three GRIB1 configs use CPTEC's native Eta names (64 variables). Fields include:
- **Pressure-level (22 levels):** geopotential height, temperature, U/V wind, vertical velocity, relative and specific humidity, potential and equivalent-potential temperature, cloud water and cloud ice
- **Surface / near-surface:** 2 m temperature, dew point, RH; 10 m and 100 m U/V wind; surface and MSL pressure (Mesinger/Eta reduction); daily max/min temperature; surface and soil temperature; soil moisture / soil wetness (surface and root-zone in the GRIB1 configs); topography and land–sea mask
- **Precipitation:** total, convective, and large-scale (non-convective); snowfall
- **Radiation & fluxes:** downward/upward short- and long-wave at the surface, net short/long-wave at TOA, latent/sensible heat flux, ground heat flux, albedo
- **Cloud:** low/medium/high and mean cloud cover; cloud base/top pressure
- **Diagnostics:** CAPE, CIN, best lifted index, precipitable water, tropopause pressure, freezing-level height and RH, max-wind-level pressure/wind; plus roughness length, wind stress, potential evaporation, and runoff in the GRIB1 configs

The Eta configurations complement CPTEC's global BAM guidance by resolving mesoscale and local-scale features, with `rjsp_01km` targeting high-resolution local forecasting over the RJ/SP corridor and `ons_40km` supporting the energy sector (streamflow / hydropower inflow guidance).

---

## Data availability
- **Is the data free?** Yes — free of charge, no registration.
- **License:** **Transitional / not yet an open license.** Data is freely accessible and usable personally today, but CPTEC/INPE's operational-server notice restricts commercial use and redistribution in published/dissemination outlets without express CPTEC/INPE authorization, and requires attribution to "CPTEC/INPE." INPE has committed under its Open Data Plan (PDA 2025–2027, Decreto 8.777/2016) to republish the Eta data as open data on dados.gov.br, scheduled ~July 2027. Not yet live on the dados.gov.br "Tempo e Clima" category as of June 2026; open reuse terms apply once it appears there.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (`ams_08km` only) and GRIB1 / `.grb` (`ams_40km`, `ons_40km`, `rjsp_01km`)
- **Official download location:**
  https://dataserver.cptec.inpe.br/dataserver_modelos/eta/
  - `ams_08km/brutos/<YYYY>/<MM>/<DD>/00/`
  - `ams_40km/brutos/<YYYY>/<MM>/<DD>/00/`
  - `ons_40km/brutos/<YYYY>/<MM>/<DD>/00/`
  - `rjsp_01km/brutos/<YYYY>/<MM>/<DD>/00/`
- **File structure note:** Each timestep ships as a separate data file plus GrADS descriptor (`.ctl`), GrADS map file for the native Eta grid (`.gmp`, GRIB1 configs), an index (`.idx`), and a wgrib-style inventory (`.inv`). Most users will want the `.grib2` / `.grb` files.

---

## Notes
- **Format split:** `ams_08km` is the only configuration distributed in GRIB2; the three others are GRIB1. GRIB1-only pipelines/decoders may be needed for the `.grb` configs (wgrib, pygrib, cfgrib all handle GRIB1).
- **Cycle:** Only the 00 UTC cycle is published for all four configurations (verified — no 12 UTC directory exists). This supersedes the earlier note that `ams_08km` ran twice daily.
- **`ams_40km` vs `ons_40km`:** Same model core, same ~40 km resolution, identical `eta_40km_` prefix — and, verified from the `.ctl` descriptors, the **same grid and domain** (144 × 157 @ 0.40°, identical bounds, same 22 levels and 64 variables; the two `.ctl` files are byte-identical apart from the `dset` name). They are therefore distinguished only by directory and **purpose** (`ons_40km` is the run for the ONS — Operador Nacional do Sistema Elétrico — energy sector), not by domain or resolution. A stray `ONS_40km_*.ctl` descriptor also appears in the `ams_40km` directory.
- **Relationship to siblings:** Complements [BAM](../../global/brazil/bam-cptec.md) (global). The Eta also feeds CPTEC's SMEC multi-model ensemble (Eta + WRF + BRAMS) and underlies the separate **ProjEta** climate-change projection product (RCP scenarios to 2099) — both distinct from this operational NWP entry; ProjEta is out of repository scope.
- **Older data:** Only the current month is served on the operational FTP/dataserver; older data requires a request to CPTEC and is subject to availability.

---

## Official documentation
- CPTEC/INPE model data server — https://dataserver.cptec.inpe.br/dataserver_modelos/eta/
- CPTEC/INPE — https://www.cptec.inpe.br/
- gov.br service page (PNT) — https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
