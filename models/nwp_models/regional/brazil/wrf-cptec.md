# WRF (CPTEC/INPE Regional Model)

## What this model is
WRF is a regional numerical weather prediction (NWP) system operated by CPTEC/INPE, based on the Weather Research and Forecasting model and run operationally for South America since 2018. It provides short- to medium-range deterministic forecasts and is publicly distributed as a single configuration over a continental South America domain at ~7 km resolution (`ams_07km`).

---

## Who runs it
- **Organization:** CPTEC/INPE (Center for Weather Forecasting and Climate Studies / National Institute for Space Research)
- **Country / region:** Brazil

---

## What area it covers
- **Coverage:** South America
- **Domain details:** Continental South America domain (`ams_07km`), distributed as a **regular latitude/longitude grid of 1019 × 1081 points at 0.07°** (~7.8 km), spanning **90.67°W–19.41°W and 57.90°S–17.70°N** (verified from the GrADS `.ctl` and confirmed against the GRIB2 grid definition: `regular_ll`, Ni=1019 × Nj=1081, 0.07° increments). The native WRF grid is presumably projected; the public product is delivered on this regular lat/lon mesh.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** WRF (Weather Research and Forecasting). The ARW core is presumed but not encoded in the output. The GRIB2 is produced by the NCEP Unified Post Processor (headers carry `centre = kwbc`, `generatingProcessIdentifier = 116`, and NCEP-style parameter names), and the field set is consistent with a standard WRF-ARW/UPP configuration.
- **Dynamical formulation:** Non-hydrostatic (WRF)
- **Convection-allowing:** No (at ~7 km the model uses parameterized convection)
- **Horizontal resolution:** ~7 km
- **Grid dimensions:** 1019 × 1081 grid points
- **Vertical levels:** 25 pressure levels in the distributed output (1000, 975, 950, 925, 900, 875, 850, 825, 800, 775, 750, 700, 650, 600, 550, 500, 450, 400, 350, 300, 250, 200, 150, 100, 50 hPa). The native model level count is not exposed in the GRIB2 (TBD).
- **Model top:** Highest distributed pressure level is 50 hPa; the native model top is not determinable from the output (TBD).
- **Forecast length:** +180 h (7.5 days)
- **Update frequency / cycles:** 1× daily (00 UTC)
- **Temporal output resolution:** Hourly (181 timesteps per run, +0 h to +180 h)

---

## Data assimilation
- **Data assimilation:** TBD

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** TBD
- **Boundary conditions:** TBD (CPTEC's regional WRF is nested in a global driver, plausibly NCEP GFS or CPTEC's BAM, but the source is not stated for the public feed)

---

## What it provides
Deterministic regional forecasts distributed as GRIB2 (89 variables per timestep, NCEP/UPP naming), including:
- **Pressure-level fields (25 levels, 1000–50 hPa):** geopotential height, temperature, relative and specific humidity, dew point, potential temperature, U/V wind, vertical velocity
- **Surface / near-surface:** 2 m temperature, dew point, RH and specific humidity; 10 m and 100 m U/V wind; surface wind gust; surface pressure and two MSLP reductions (PRMSL and Eta-reduction MSLET); surface temperature, geopotential height, and specific humidity; visibility; PBL height
- **Precipitation:** total, convective (ACPCP), and large-scale/non-convective (NCPCP)
- **Radiation & surface fluxes:** downward/upward long- and short-wave flux, latent and sensible heat flux, ground heat flux, albedo
- **Cloud:** total, low, middle, and high cloud cover; cloud base/ceiling/top height; cloud-top temperature
- **Convective / severe-weather diagnostics:** CAPE and CIN (surface and 90-0/180-0/255-0 mb layers), lifted index (best 4-layer and surface), storm-relative helicity (0–1 km, 0–3 km), U/V storm motion (0–6 km), composite and 1 km/4 km reflectivity, precipitable water
- **Land surface:** soil temperature and volumetric/liquid soil moisture (4 layers: 0–10, 10–40, 40–100, 100–200 cm), snow cover, water-equivalent snow depth, surface and baseflow runoff, land–sea mask
- **Levels of interest:** 0 °C isotherm height/RH, tropopause height, max-wind-level height/pressure/wind

---

## Data availability
- **Is the data free?** Yes (free of charge, no registration)
- **License:** Creative Commons Attribution (version not specified). Catalogued as open on the Portal Brasileiro de Dados Abertos as "Modelo WRF AMS 07km" (catalogued 11 August 2026; daily updates), with INPE's DIPTC/DIMNT listed as the responsible technical area. Attribution to CPTEC/INPE applies.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://dataserver.cptec.inpe.br/dataserver_modelos/wrf/ams_07km/brutos/
  - `<YYYY>/<MM>/<DD>/00/` contains one GRIB2 file per timestep, `WRF_cpt_07KM_<init>_<valid>.grib2` (`<init>`/`<valid>` as `YYYYMMDDHH`), plus a GrADS descriptor (`.ctl`), an index (`.grib2.idx`), and a wgrib-style inventory (`.inv`). Most users will want the `.grib2` files.

---

## Notes
- **Archive:** The dataserver holds complete daily runs back to 1 January 2023 (verified October 2026). This conflicts with the gov.br PNT service page, which says only the current month is online and older data must be requested; the dataserver is treated as authoritative here.
- **Cycle:** Only the 00 UTC cycle is published (no 12 UTC directory exists). This supersedes the earlier note of a 2× daily cycle and ~72 h length in the previous "CPTEC/INPE Regional Model (7 km)" entry.
- **Tooling:** CPTEC maintains a Python package, CPTECModels, for reading its model output, linked from the dados.gov.br listing (https://cptec-model.readthedocs.io/en/latest/usage.html#modelos-disponiveis).
- **dados.gov.br listing quirks:** The dataset page names CGU as the publishing organization in its header, while listing INPE's DIPTC/DIMNT as the responsible technical area. Its description text begins "Modelo numérico BRAMS…", an apparent copy-paste error; the title, resource, and filename pattern are all WRF.
- **Naming on gov.br:** The gov.br PNT service page lists this as "WRF 05km", which conflicts with the ~7 km resolution of the `ams_07km` feed; the dataserver path and filenames are treated as authoritative here.
- **Relationship to siblings:** Run alongside the [Eta](./eta-cptec.md) and [BRAMS](./brams-cptec.md) regional systems, and complements [BAM](../../global/brazil/bam-cptec.md) (global). WRF also contributes to CPTEC's SMEC multi-model ensemble (Eta + WRF + BRAMS).

---

## Official documentation
- CPTEC/INPE model data server: https://dataserver.cptec.inpe.br/dataserver_modelos/wrf/ams_07km/
- Portal Brasileiro de Dados Abertos dataset: https://dados.gov.br/dados/conjuntos-dados/wrf
- CPTEC numerical forecast pages: https://previsaonumerica.cptec.inpe.br/wrf/aprec/ams/
- CPTECModels documentation: https://cptec-model.readthedocs.io/en/latest/usage.html#modelos-disponiveis
- CPTEC/INPE: https://www.cptec.inpe.br/
- gov.br service page (PNT): https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
