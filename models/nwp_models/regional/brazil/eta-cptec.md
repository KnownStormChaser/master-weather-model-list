# ETA (CPTEC/INPE Regional Model)

## What this model is
The Eta model is a regional numerical weather prediction (NWP) system operated by CPTEC/INPE, built on the Eta step-mountain vertical coordinate. It provides short- to medium-range deterministic forecasts over South America and is publicly distributed in three active configurations that differ by resolution, purpose, and file format. A fourth, high-resolution local configuration over Rio de Janeiro / São Paulo stopped publishing new runs in August 2026 (see **Recent version history**).

---

## Who runs it
- **Organization:** CPTEC/INPE (Center for Weather Forecasting and Climate Studies / National Institute for Space Research)
- **Country / region:** Brazil

---

## What area it covers
- **Coverage:** South America
- **Domain details:** All configurations are distributed as regular lat/lon grids (verified from the GrADS `.ctl` descriptors):
  - `ams_08km`: 875 × 931 @ 0.08° (~8.9 km), 90.00°W–20.08°W, 55.00°S–19.40°N
  - `ams_40km`: 144 × 157 @ 0.40° (~44 km), 83.00°W–25.80°W, 50.20°S–12.20°N
  - `ons_40km`: **identical grid to `ams_40km`** (144 × 157 @ 0.40°, same bounds), run separately for the ONS energy sector
  - `rjsp_01km` (no new runs since 27 August 2026): 450 × 280 @ 0.01° (~1.1 km), 46.30°W–41.81°W, 24.40°S–21.61°S (RJ/SP corridor)

---

## Model configurations

| Config | Domain | Horizontal res. | Forecast length | Output step | Cycle | Format | Filename pattern |
|---|---|---|---|---|---|---|---|
| `ams_08km` | South America | ~8 km | +264 h (11 days) | Hourly | 00 UTC, 1×/day | **GRIB2** | `Eta_ams_08km_<init>_<valid>.grib2` |
| `ams_40km` | South America | ~40 km | +264 h (11 days) | Hourly | 00 UTC, 1×/day | GRIB1 (`.grb`) | `eta_40km_<init>+<valid>.grb` |
| `ons_40km` | South America (ONS run) | ~40 km | +264 h (11 days) | Hourly | 00 UTC, 1×/day | GRIB1 (`.grb`) | `eta_40km_<init>+<valid>.grb` |
| `rjsp_01km` *(stopped Aug 2026)* | Rio de Janeiro / São Paulo | ~1 km | +72 h (3 days) | Hourly | 00 UTC, 1×/day | GRIB1 (`.grb`) | `eta_01km_<init>+<valid>.grb` |

(`<init>` and `<valid>` are `YYYYMMDDHH`. Note that `ams_40km` and `ons_40km` use the **same** `eta_40km_` filename prefix and are distinguished only by directory path.)

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** Eta (eta step-mountain vertical coordinate)
- **Dynamical formulation:** TBD (classic Eta is hydrostatic; not confirmed for the current CPTEC configuration)
- **Convection-allowing:** No for the active configurations (8 km and 40 km use parameterized convection). The discontinued `rjsp_01km` (~1 km) was the only convection-allowing configuration.
- **Horizontal resolution:** ~8 km (`ams_08km`); ~40 km (`ams_40km`, `ons_40km`); ~1 km (`rjsp_01km`, discontinued)
- **Vertical levels:** All configurations distribute **22 pressure levels**: 1020, 1000, 950, 925, 900, 850, 800, 750, 700, 650, 600, 550, 500, 450, 400, 350, 300, 250, 200, 150, 100, 50 hPa (verified from the `.ctl` `zdef`). The native Eta step-mountain level count is not exposed in the output (TBD).
- **Model top:** Highest distributed pressure level is 50 hPa; the native model top is not determinable from the output (TBD).
- **Forecast length:** +264 h (11 days) for `ams_08km`, `ams_40km`, `ons_40km`; +72 h (3 days) for `rjsp_01km`
- **Update frequency / cycles:** 1× daily (00 UTC)
- **Temporal output resolution:** Hourly

---

## Data assimilation
- **Data assimilation:** TBD

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** TBD
- **Boundary conditions:** TBD (CPTEC's regional Eta is typically nested in a global driver, plausibly CPTEC's BAM, but this is not stated for the public feed)

---

## What it provides
Deterministic regional forecasts on 22 pressure levels plus surface and near-surface fields. The two formats use different naming conventions: `ams_08km` (GRIB2) uses NCEP/UPP-style names (48 fields per the `.ctl` descriptors); the GRIB1 configs use CPTEC's native Eta names (64 fields). Fields include:
- **Pressure levels (22):** geopotential height, temperature, U/V wind, vertical velocity, relative and specific humidity, potential and equivalent-potential temperature, cloud water and cloud ice
- **Surface / near-surface:** 2 m temperature, dew point, RH; 10 m and 100 m U/V wind; surface and MSL pressure (Mesinger/Eta reduction); daily max/min temperature; surface and soil temperature; soil moisture / soil wetness (surface and root zone in the GRIB1 configs); surface wind stress; topography and land–sea mask
- **Precipitation:** total, convective, and large-scale (non-convective); snowfall
- **Radiation & fluxes:** downward/upward short- and long-wave at the surface, net short/long-wave at TOA, latent/sensible heat flux, ground heat flux, albedo
- **Cloud:** low/medium/high and mean cloud cover; cloud base/top pressure
- **Diagnostics:** CAPE, CIN, best lifted index, precipitable water, tropopause pressure, freezing-level height and RH, max-wind-level pressure/wind; plus roughness length, potential evaporation, and runoff

`ons_40km` supports the energy sector (streamflow and hydropower inflow guidance for the national grid operator).

---

## Data availability
- **Is the data free?** Yes (free of charge, no registration)
- **License:** No license or terms of use are stated on the CPTEC dataserver. INPE's Open Data Plan (PDA 2025–2027, Decreto 8.777/2016) lists the Eta data as open ("base aberta") but not yet catalogued on the Portal Brasileiro de Dados Abertos, with publication scheduled for ~July 2027. CPTEC's sibling WRF and BRAMS datasets were catalogued there in August 2026 under Creative Commons Attribution, so the same terms are likely once Eta is published, but this is not yet confirmed.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (`ams_08km`) and GRIB1 / `.grb` (`ams_40km`, `ons_40km`, `rjsp_01km`)
- **Official download location:**
  https://dataserver.cptec.inpe.br/dataserver_modelos/eta/
  - `ams_08km/brutos/<YYYY>/<MM>/<DD>/00/`
  - `ams_40km/brutos/<YYYY>/<MM>/<DD>/00/`
  - `ons_40km/brutos/<YYYY>/<MM>/<DD>/00/`
  - `rjsp_01km/brutos/<YYYY>/<MM>/<DD>/00/` (archive only; last run 27 August 2026)
- **File structure note:** Each timestep ships as a separate data file plus a GrADS descriptor (`.ctl`), a GrADS map file for the native Eta grid (`.gmp`, GRIB1 configs), an index (`.idx`), and a wgrib-style inventory (`.inv`). Most users will want the `.grib2` / `.grb` files.

---

## Notes
- **Archive:** The dataserver holds complete daily runs going back several years (verified October 2026): `ams_40km` and `ons_40km` from July 2020, `ams_08km` from July 2021, and `rjsp_01km` from May 2020 to August 2026. This conflicts with the gov.br PNT service page, which says only the current month is online and older data must be requested; the dataserver is treated as authoritative here.
- **Format split:** `ams_08km` is the only configuration distributed in GRIB2; the others are GRIB1. wgrib, pygrib, and cfgrib all handle GRIB1.
- **Cycle:** Only the 00 UTC cycle is published (no 12 UTC directory exists). This supersedes the earlier note that `ams_08km` ran twice daily.
- **`ams_40km` vs `ons_40km`:** Same model core, same ~40 km resolution, identical `eta_40km_` prefix, and, verified from the `.ctl` descriptors, the **same grid and domain** (144 × 157 @ 0.40°, identical bounds, same 22 levels and 64 variables; the two `.ctl` files are byte-identical apart from the `dset` name). They are distinguished only by directory and **purpose** (`ons_40km` is the run for the ONS, Operador Nacional do Sistema Elétrico), not by domain or resolution. A stray `ONS_40km_*.ctl` descriptor also appears in the `ams_40km` directory.
- **Tooling:** CPTEC maintains a Python package, CPTECModels, for reading its model output (https://cptec-model.readthedocs.io/en/latest/usage.html#modelos-disponiveis).
- **Relationship to siblings:** Run alongside the [WRF](./wrf-cptec.md) and [BRAMS](./brams-cptec.md) regional systems, and complements [BAM](../../global/brazil/bam-cptec.md) (global). The Eta also feeds CPTEC's SMEC multi-model ensemble (Eta + WRF + BRAMS) and underlies the separate **ProjEta** climate-change projection product (RCP scenarios to 2099), which is out of repository scope.

---

## Recent version history
- **Between June and October 2026:** In the `ams_08km` GRIB2 descriptors, three surface fields previously listed under unnamed local parameter codes (`var02238sfc`, `var02239sfc`, `var10194sfc`) now appear as named fields (`RNSGsfc`, `USSTsfc`, `VSSTsfc`). The field count (48), grid, and levels are unchanged, so this appears to be a relabelling rather than new output.
- **27 August 2026:** `rjsp_01km` published its last run. The directory remains on the server, so this may be a pause rather than a retirement; it has not been confirmed by CPTEC. The ~15 km BRAMS streams stopped the same week.
- **28 August – 9 September 2026:** `ams_40km` and `ons_40km` published no runs. Both resumed on 10 September 2026 with the same grid, levels, variables, and +264 h range. `ams_08km` was unaffected.

---

## Official documentation
- CPTEC/INPE model data server: https://dataserver.cptec.inpe.br/dataserver_modelos/eta/
- CPTECModels documentation: https://cptec-model.readthedocs.io/en/latest/usage.html#modelos-disponiveis
- CPTEC/INPE: https://www.cptec.inpe.br/
- gov.br service page (PNT): https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
