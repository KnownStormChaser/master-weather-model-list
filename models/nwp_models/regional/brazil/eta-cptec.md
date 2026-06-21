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
- **Domain details:** Two continental South America domains (`ams_08km`, `ams_40km`), one energy-sector domain run for the national grid operator (`ons_40km`), and one high-resolution local domain over the RJ/SP corridor (`rjsp_01km`). Exact grid bounds for each domain are TBD.

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
- **Vertical levels:** TBD
- **Model top:** TBD
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
Deterministic regional forecasts of core atmospheric variables, including:
- Near-surface temperature, wind, and humidity
- Atmospheric pressure and geopotential
- Precipitation
- Mid- and upper-level circulation fields

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
- **`ams_40km` vs `ons_40km`:** Same model core, same ~40 km resolution, and identical `eta_40km_` filename prefix; they are separate products distinguished only by directory and domain/purpose (`ons_40km` is the configuration run for the ONS — Operador Nacional do Sistema Elétrico — energy sector). A stray `ONS_40km_*.ctl` descriptor also appears in the `ams_40km` directory.
- **Relationship to siblings:** Complements [BAM](../../global/brazil/bam-cptec.md) (global). The Eta also feeds CPTEC's SMEC multi-model ensemble (Eta + WRF + BRAMS) and underlies the separate **ProjEta** climate-change projection product (RCP scenarios to 2099) — both distinct from this operational NWP entry; ProjEta is out of repository scope.
- **Older data:** Only the current month is served on the operational FTP/dataserver; older data requires a request to CPTEC and is subject to availability.

---

## Official documentation
- CPTEC/INPE model data server — https://dataserver.cptec.inpe.br/dataserver_modelos/eta/
- CPTEC/INPE — https://www.cptec.inpe.br/
- gov.br service page (PNT) — https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
