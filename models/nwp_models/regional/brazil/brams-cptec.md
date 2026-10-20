# BRAMS (CPTEC/INPE Regional Model)

## What this model is
BRAMS (Brazilian developments on the Regional Atmospheric Modeling System) is a regional numerical weather prediction (NWP) model operated by CPTEC/INPE, derived from the RAMS model and adapted for tropical and South American conditions. It provides short- to medium-range deterministic forecasts over South America and is publicly distributed as a single configuration at ~8 km resolution (`ams_08km`).

BRAMS can also run coupled with atmospheric chemistry and aerosol transport (CCATT-BRAMS). CPTEC previously published that configuration as a separate ~15 km air-quality stream, which stopped in August 2026 (see **Recent version history**). The only BRAMS output still distributed is meteorological.

---

## Who runs it
- **Organization:** CPTEC/INPE (Center for Weather Forecasting and Climate Studies / National Institute for Space Research)
- **Country / region:** Brazil

---

## What area it covers
- **Coverage:** South America
- **Domain details:** Regular latitude/longitude grid of **978 × 1009** points at **0.0755° lon × 0.0668° lat** (~8 km), spanning **92.87°W–19.13°W and 51.74°S–15.63°N** (verified from the GrADS `.ctl` descriptors).

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** BRAMS (RAMS-derived)
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** No (at ~8 km the model uses parameterized convection)
- **Horizontal resolution:** ~8 km
- **Grid dimensions:** 978 × 1009 grid points
- **Vertical levels:** 25 pressure levels in the distributed output (1000, 975, 950, 925, 900, 875, 850, 825, 800, 775, 750, 700, 650, 600, 550, 500, 450, 400, 350, 300, 250, 200, 150, 100, 50 hPa). The native model level count is not exposed in the output (TBD).
- **Model top:** Highest distributed level is 50 hPa; native model top TBD.
- **Forecast length:** +192 h (8 days)
- **Update frequency / cycles:** 1× daily (00 UTC)
- **Temporal output resolution:** Hourly (193 timesteps per run, +0 h to +192 h)

---

## Data assimilation
- **Data assimilation:** TBD

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** TBD
- **Boundary conditions:** TBD (BRAMS is nested in a global driver, plausibly CPTEC's BAM or NCEP GFS, but the source is not stated for the public feed)

---

## What it provides
Deterministic forecasts of 52 fields (per the `.ctl` descriptors), including:
- Temperature at 2 m (plus 2 m maximum and minimum) and on pressure levels
- U/V wind at 10 m and on pressure levels, plus vertical velocity
- Geopotential height, relative humidity, dew point (2 m), and equivalent potential temperature
- Mean sea-level pressure (standard and METAR formulations) and surface pressure
- Total and convective precipitation, precipitable water
- CAPE, CIN, and planetary boundary layer height
- Low, mid, and high cloud cover; cloud and vapor mixing ratios
- Surface radiation and energy fluxes (net shortwave/longwave, upward longwave, latent and sensible heat)
- Land-surface fields: albedo, soil moisture (4 layers), LAI and NDVI (4 patches), vegetation temperature (4 patches), and water-surface temperature

---

## Data availability
- **Is the data free?** Yes (free of charge, no registration)
- **License:** Creative Commons Attribution (version not specified). Catalogued as open on the Portal Brasileiro de Dados Abertos as "Modelo BRAMS AMS 08km" (catalogued 11 August 2026; daily updates), with INPE's DIPTC/DIMNT listed as the responsible technical area. Attribution to CPTEC/INPE applies.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://dataserver.cptec.inpe.br/dataserver_modelos/brams/ams_08km/brutos/
  - `<YYYY>/<MM>/<DD>/00/` contains one GRIB2 file per timestep, `BRAMS_ams_08km_<init>_<valid>.grib2` (`<init>`/`<valid>` as `YYYYMMDDHH`), plus a GrADS descriptor (`.ctl`), an index (`.idx`), and a wgrib-style inventory (`.inv`). Most users will want the `.grib2` files.

---

## Notes
- **Tooling:** CPTEC maintains a Python package, CPTECModels, for reading its model output, linked from the dados.gov.br listing (https://cptec-model.readthedocs.io/en/latest/usage.html#modelos-disponiveis).
- **dados.gov.br listing quirks:** The dataset page names CGU as the publishing organization in its header, while listing INPE's DIPTC/DIMNT as the responsible technical area. Its description says forecasts run "up to 7 days", which predates the current +192 h range.
- **Resolution conflict:** The gov.br PNT service page lists "BRAMS Ambiental 20 km", which matches neither the current ~8 km feed nor the discontinued ~15 km streams. The dataserver paths and descriptors are treated as authoritative here.
- **Air-quality lineage:** BRAMS's coupled chemistry configuration (CCATT-BRAMS) was previously published as the `ams_15km` streams, and this model was catalogued under air quality on that basis. With those streams stopped, it is filed here as regional NWP. If the chemistry streams return, an air-quality entry would be warranted again.
- **Relationship to siblings:** Run alongside the [Eta](./eta-cptec.md) and [WRF](./wrf-cptec.md) regional systems, and complements [BAM](../../global/brazil/bam-cptec.md) (global). BRAMS also contributes to CPTEC's SMEC multi-model ensemble (Eta + WRF + BRAMS).
- **Archive:** The dataserver holds complete daily `ams_08km` runs back to 1 January 2025 (verified October 2026). This conflicts with the gov.br PNT service page, which says only the current month is online and older data must be requested; the dataserver is treated as authoritative here.

---

## Recent version history
- **28 August 2026:** The `ams_08km` forecast range changed from −3 h to +180 h (184 hourly timesteps, starting 3 h before the 00 UTC initialization) to +0 h to +192 h (193 hourly timesteps). Grid, pressure levels, and variable set are unchanged (verified by comparing descriptors from 27 August and 19 October 2026).
- **Late August 2026:** The ~15 km CCATT-BRAMS streams stopped publishing new runs. The last `ams_15km/gases` run is 26 August 2026, and the last `ams_15km/previsao` run is 27 August 2026. Both were GrADS binary (`.gra` + `.ctl`) on a 473 × 548 grid at ~15 km, with `previsao` running to +72 h and `gases` to +24 h at 3-hourly steps. Both carried the same set of meteorology plus chemistry and aerosol fields (including CO, NO, NO₂, O₃, HNO₃, PM2.5, and aerosol optical thickness at 500/550 nm). The directories remain on the server, so this may be a pause rather than a retirement; it has not been confirmed by CPTEC.

---

## Official documentation
- CPTEC/INPE model data server: https://dataserver.cptec.inpe.br/dataserver_modelos/brams/
- Portal Brasileiro de Dados Abertos dataset: https://dados.gov.br/dados/conjuntos-dados/brams
- BRAMS model site: http://brams.cptec.inpe.br/
- CPTEC numerical forecast pages: https://previsaonumerica.cptec.inpe.br/brams/aprec/ams/
- CPTECModels documentation: https://cptec-model.readthedocs.io/en/latest/usage.html#modelos-disponiveis
- gov.br service page (PNT): https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
