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
- **Model system / core:** WRF (Weather Research and Forecasting); ARW core presumed but not definitively encoded in the output. The GRIB2 is produced by the NCEP Unified Post Processor (headers carry `centre = kwbc`, `generatingProcessIdentifier = 116`, and NCEP-style parameter names), and the field set is consistent with a standard WRF-ARW/UPP configuration.
- **Dynamical formulation:** Non-hydrostatic (WRF)
- **Convection-allowing:** No — at ~7 km the model uses parameterized convection
- **Horizontal resolution:** ~7 km
- **Vertical levels:** 25 pressure levels in the distributed output (1000, 975, 950, 925, 900, 875, 850, 825, 800, 775, 750, 700, 650, 600, 550, 500, 450, 400, 350, 300, 250, 200, 150, 100, 50 hPa). The native model level count is not exposed in the GRIB2 — unconfirmed.
- **Model top:** Highest distributed pressure level is 50 hPa; the native model top is higher but not determinable from the output — unconfirmed.
- **Forecast length:** +180 h (7.5 days)
- **Update frequency / cycles:** 1× daily (00 UTC)
- **Temporal output resolution:** Hourly

---

## Data assimilation (optional)
- **Data assimilation:** TBD

---

## Initial and boundary conditions
- **Initial / boundary conditions:** TBD — CPTEC's regional WRF is nested in a global driver (plausibly GFS or CPTEC's BAM), but the source is unconfirmed for the public feed.

---

## What it provides
Deterministic regional forecasts distributed as GRIB2 (89 variables per timestep, NCEP/UPP naming), including:
- **Pressure-level fields (25 levels, 1000–50 hPa):** geopotential height, temperature, relative and specific humidity, dew point, potential temperature, U/V wind, vertical velocity
- **Surface / near-surface:** 2 m temperature, dew point, RH and specific humidity; 10 m and 100 m U/V wind; surface wind gust; surface pressure and two MSLP reductions (PRMSL and Eta-reduction MSLET); surface temperature, geopotential height, and specific humidity; visibility; PBL height
- **Precipitation:** total, convective (ACPCP), and large-scale/non-convective (NCPCP) — the separate convective field confirms parameterized convection
- **Radiation & surface fluxes:** downward/upward long- and short-wave flux, latent and sensible heat flux, ground heat flux, albedo
- **Cloud:** total, low, middle, and high cloud cover; cloud base/ceiling/top height; cloud-top temperature and visibility
- **Convective / severe-weather diagnostics:** CAPE and CIN (surface and 90-0/180-0/255-0 mb layers), lifted index (best 4-layer and surface), storm-relative helicity (0–1 km, 0–3 km), U/V storm motion (0–6 km), composite and 1 km/4 km reflectivity, precipitable water
- **Land-surface:** soil temperature and volumetric/liquid soil moisture (4 layers: 0–10, 10–40, 40–100, 100–200 cm), snow cover, water-equivalent snow depth, surface and baseflow runoff, land–sea mask
- **Levels of interest:** 0 °C isotherm height/RH, tropopause height, max-wind-level height/pressure/wind

The WRF configuration is one of CPTEC's two operational regional NWP systems over South America, run alongside the [Eta](./eta-cptec.md) configurations.

---

## Data availability
- **Is the data free?** Yes — free of charge, no registration.
- **License:** **Transitional / not yet an open license.** Data is freely accessible and usable personally today, but CPTEC/INPE's operational-server notice restricts commercial use and redistribution in published/dissemination outlets without express CPTEC/INPE authorization, and requires attribution to "CPTEC/INPE." INPE has committed under its Open Data Plan (PDA 2025–2027, Decreto 8.777/2016) to republish the WRF data as open data on dados.gov.br, scheduled ~June 2026. As of late June 2026 it is not yet live on the dados.gov.br "Tempo e Clima" category; open reuse terms apply once it appears there.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://dataserver.cptec.inpe.br/dataserver_modelos/wrf/ams_07km/
  - `ams_07km/brutos/<YYYY>/<MM>/<DD>/00/`
- **File structure note:** Each timestep ships as a separate GRIB2 file (`WRF_cpt_07KM_<init>_<valid>.grib2`, with `<init>`/`<valid>` as `YYYYMMDDHH`) plus a GrADS descriptor (`.ctl`), an index (`.grib2.idx`), and a wgrib-style inventory (`.inv`). Most users will want the `.grib2` files.

---

## Notes
- **Cycle:** Only the 00 UTC cycle is published (verified — no 12 UTC directory exists). This supersedes the earlier note of a 2× daily cycle and ~72 h length in the previous "CPTEC/INPE Regional Model (7 km)" entry; the public feed runs once daily to +180 h.
- **Relationship to siblings:** Run alongside the [Eta](./eta-cptec.md) regional configurations as CPTEC's second operational regional NWP system, and complements [BAM](../../global/brazil/bam-cptec.md) (global). WRF also contributes to CPTEC's SMEC multi-model ensemble (Eta + WRF + BRAMS).
- **Naming on gov.br:** The gov.br PNT service page lists this as "WRF 05km," which conflicts with the ~7 km resolution of the `ams_07km` operational feed; the dataserver path and filenames are treated as authoritative here.
- **Older data:** Only the current month is served on the operational FTP/dataserver; older data requires a request to CPTEC and is subject to availability.

---

## Official documentation
- CPTEC/INPE model data server — https://dataserver.cptec.inpe.br/dataserver_modelos/wrf/ams_07km/
- CPTEC/INPE — https://www.cptec.inpe.br/
- gov.br service page (PNT) — https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
