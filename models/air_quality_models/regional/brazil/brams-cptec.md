# BRAMS (CPTEC/INPE Regional Atmosphere–Chemistry Model)

## What this model is
BRAMS (Brazilian developments on the Regional Atmospheric Modeling System) is a regional model operated by CPTEC/INPE that serves a dual role: numerical weather prediction and air-quality / atmospheric-composition forecasting over South America. Its composition capability comes from the coupled aerosol-and-tracer-transport configuration (CATT-BRAMS), which forecasts biomass-burning smoke and trace gases (notably CO and particulate matter) online within the meteorological model. It is publicly distributed in two domains (~8 km and ~15 km) and three output streams.

---

## Who runs it
- **Organization:** CPTEC/INPE (Center for Weather Forecasting and Climate Studies / National Institute for Space Research)
- **Country / region:** Brazil

---

## What area it covers
- **Coverage:** South America
- **Domain details:** Two continental South America domains, both regular lat/lon grids (verified from the GRIB2 and GrADS `.ctl` descriptors):
  - `ams_08km`: **978 × 1009** @ **0.0755° lon × 0.0668° lat** (~8 km) — **92.87°W–19.13°W, 51.74°S–15.63°N**
  - `ams_15km`: **473 × 548** @ **0.1431° lon × 0.1260° lat** (~15 km) — **88.19°W–20.63°W, 52.65°S–16.29°N** (same grid for both the `previsao` and `gases` streams)

---

## Public configurations and output streams

| Stream | Domain | Res. | Content | Forecast length | Output step | Format | Filename pattern |
|---|---|---|---|---|---|---|---|
| `ams_08km` | South America | ~8 km | Meteorology | −3 h to +180 h (7.5 days) | Hourly | **GRIB2** | `BRAMS_ams_08km_<init>_<valid>.grib2` |
| `ams_15km/previsao` | South America | ~15 km | Meteorology + chemistry | +0 h to +72 h (3 days) | 3-hourly | GrADS (`.gra`+`.ctl`) | `profile_<init>-A-<valid>-g1.gra` |
| `ams_15km/gases` | South America | ~15 km | Meteorology + chemistry | +0 h to +24 h (1 day) | 3-hourly | GrADS (`.gra`+`.ctl`) | `profile_<init>G-A-<valid>-g1.gra` |

(`<init>` = `YYYYMMDDHH`; `<valid>` in the GrADS streams is `YYYY-MM-DD-HHMMSS`. The `G` in the gases filenames marks the chemistry stream; `-g1` denotes BRAMS grid 1. All three streams run once daily at 00 UTC.)

---

## Basic details
- **Model type:** Regional coupled meteorology–chemistry (NWP + air quality)
- **Model system / core:** BRAMS (RAMS-derived regional model); chemistry via **CCATT-BRAMS** (Coupled Chemistry-Aerosol-Tracer Transport). The distributed `ams_15km` species include gas-phase photochemistry (O₃, NO, NO₂, HNO₃, CO with online source terms) beyond pure aerosol/tracer transport, indicating the coupled-chemistry (CCATT) configuration rather than transport-only CATT-BRAMS — file-verified from the GrADS descriptors
- **Horizontal resolution:** ~8 km (`ams_08km`) and ~15 km (`ams_15km`)
- **Vertical levels (distributed output):** `ams_08km` GRIB2 on **25 pressure levels** (1000, 975, 950, 925, 900, 875, 850, 825, 800, 775, 750, 700, 650, 600, 550, 500, 450, 400, 350, 300, 250, 200, 150, 100, 50 hPa); `ams_15km` GrADS on **32 terrain-following height levels** (~39 m to ~10,290 m). Native model level count is not exposed in the output (TBD).
- **Model top (distributed output):** 50 hPa (`ams_08km`); ~10.3 km (`ams_15km`). Native model lid not exposed (TBD).
- **Forecast length:** +180 h (`ams_08km`); +72 h (`ams_15km/previsao`); +24 h (`ams_15km/gases`)
- **Update frequency / cycles:** 1× daily (00 UTC) — all streams
- **Temporal output resolution:** Hourly (`ams_08km`); 3-hourly (`ams_15km` previsao and gases)

---

## Meteorological driver
- **Driving NWP model:** BRAMS is itself the meteorological model (composition is computed inside it, not driven by a separate met model).
- **Coupling:** Online — CATT-BRAMS transports tracers/aerosols using the same dynamical core that produces the meteorology.
- **Initial / boundary conditions:** TBD — the regional BRAMS is nested in a global driver (plausibly CPTEC's BAM or GFS), unconfirmed for the public feed.

---

## Chemistry and aerosols
- **Configuration:** CATT-BRAMS (Coupled Aerosol and Tracer Transport coupled to BRAMS), per the published model design (Freitas et al., 2009; Longo et al., 2013).
- **Primary focus:** Biomass-burning emissions and transport over South America — carbon monoxide (CO) and particulate matter / smoke tracers.
- **Gas-phase chemical mechanism:** Not stated in the descriptors (TBD). The distributed gas-phase species (O₃, NO, NO₂, HNO₃, CO) are consistent with a reduced photochemical mechanism (RELACS/RACM family typically used in CCATT-BRAMS), but the mechanism name is not confirmed by the files.
- **Distributed chemical species / tracers** (from the `ams_15km` GrADS `.ctl`, 3D on 32 levels unless noted): CO, NO, NO₂, O₃, HNO₃ (gas-phase, ppbv); PM2.5 (urban + biomass-burning, µg/m³), plus vertically integrated PMINT and PM2.5 wet deposition; separate tracer families — biomass-burning (bburn2/bburn3), urban (urban2/urban3), marine (marin1); online CO/NO source terms (CO_SRC, NO_SRC); and aerosol optical thickness at 500 nm and 550 nm (AOT500/AOT550). Full internal species count not exposed (TBD).
- **Aerosol treatment / components:** Output includes PM2.5 (combined urban + biomass-burning), AOT at 500/550 nm, and PM2.5 wet deposition; the full internal aerosol scheme is not documented in the feed (TBD).

---

## Emissions
- **Wildfire / biomass-burning emissions:** Per the CATT-BRAMS design, derived from satellite fire detection via the Brazilian Biomass Burning Emission Model (3BEM) / PREP-CHEM-SRC. Current operational inventory details are not documented in the public feed (TBD).
- **Anthropogenic / biogenic inventories:** TBD.

---

## Data assimilation
- **Assimilates AQ observations:** TBD.

---

## What it provides
- **`ams_08km` (GRIB2, meteorology only — 50 fields):** temperature (surface/2 m and on 25 pressure levels), U/V wind (10 m and pressure levels), vertical velocity, geopotential height, RH and dew point, MSLP (standard and METAR formulations) and surface pressure, total/convective precipitation, CAPE/CIN, PBL height, low/mid/high cloud cover, cloud and vapor mixing ratios, radiation fluxes (net SW/LW, upward LW, latent/sensible heat), precipitable water, albedo, soil moisture and LAI/NDVI (4 patches each), and vegetation/water-surface temperatures. No chemistry fields.
- **`ams_15km` (GrADS, both `previsao` and `gases` — 68 fields):** the meteorological set above (on 32 height levels) **plus** the CCATT chemistry/aerosol species — CO, NO, NO₂, O₃, HNO₃, PM2.5, AOT500/550, biomass-burning/urban/marine tracers, and CO/NO source terms (see *Chemistry and aerosols*). Both streams carry the identical set, differing only in horizon (+72 h vs +24 h).

---

## Data availability
- **Is the data free?** Yes — free of charge, no registration.
- **License:** **Transitional / not yet an open license.** Data is freely accessible and usable personally today, but CPTEC/INPE's operational-server notice restricts commercial use and redistribution in published/dissemination outlets without express CPTEC/INPE authorization, and requires attribution to "CPTEC/INPE." INPE has committed under its Open Data Plan (PDA 2025–2027, Decreto 8.777/2016) to republish BRAMS as open data on dados.gov.br, scheduled ~June 2026. As of late June 2026 it is not yet live on the dados.gov.br "Tempo e Clima" category; open reuse terms apply once it appears there. Note the PDA entry references the ~8 km BRAMS specifically; the open-data status of the `ams_15km` GrADS streams is less clear.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (`ams_08km`) and GrADS binary (`.gra` data + `.ctl` descriptor) for `ams_15km`. The GrADS streams require GrADS-compatible tooling (e.g., GrADS/OpenGrADS, or the `.ctl` read via xgrads/pygrads) rather than a standard GRIB reader; individual `.gra` timesteps are large (~0.8 GB).
- **Official download location:**
  https://dataserver.cptec.inpe.br/dataserver_modelos/brams/
  - `ams_08km/brutos/<YYYY>/<MM>/<DD>/00/`
  - `ams_15km/brutos/previsao/<YYYY>/<MM>/<DD>/00/`
  - `ams_15km/brutos/gases/<YYYY>/<MM>/<DD>/00/`

---

## Notes
- **Format split & tooling:** Only `ams_08km` is GRIB2 and broadly accessible; `ams_15km` is GrADS binary. The `.gra`/`.ctl` pair is still structured gridded data but is less universal — flag for users expecting GRIB/NetCDF.
- **`ams_08km` output offset:** Hourly output is provided from −3 h (3 h before the 00 UTC init) out to +180 h.
- **Stream split within `ams_15km`:** `previsao` and `gases` are **not** a meteorology-vs-chemistry split — both distribute the **identical 68-variable set** (meteorology *and* the full CCATT chemistry/aerosol species), verified from the `.ctl` descriptors and confirmed by reading the `.gra` binaries (CO, PM2.5, O₃, NO₂ are populated with physical values in both). They differ only in **forecast length** (`previsao` +72 h, `gases` +24 h) and the `G` filename marker. Why two identically-structured streams are maintained is unclear (flag).
- **Resolution conflict:** The gov.br PNT service page lists "BRAMS Ambiental 20 km," which conflicts with the dataserver's ~8 km and ~15 km domains; the dataserver paths/filenames are treated as authoritative here.
- **Dual role:** BRAMS is also one of CPTEC's regional NWP systems; the `ams_08km` GRIB2 stream is essentially meteorological. This entry is filed under air quality on the basis of the CATT-BRAMS composition capability — cross-reference from the NWP side if useful.
- **Relationship to siblings:** Contributes to CPTEC's SMEC multi-model ensemble (Eta + WRF + BRAMS); run alongside the [Eta](../../../nwp_models/regional/brazil/eta-cptec.md) and [WRF](../../../nwp_models/regional/brazil/wrf-cptec.md) regional systems and the global [BAM](../../../nwp_models/global/brazil/bam-cptec.md).
- **Stale server inventories:** Plain-text directory dumps (e.g., `previsao.txt`, `gases.txt`, `lista_*.txt`) left in the `ams_15km` tree reflect a 2021–2022 archive snapshot, not current availability; current runs are present and daily (with occasional missing days).
- **Older data:** Only recent data is served operationally; older data requires a request to CPTEC and is subject to availability.

---

## Official documentation
- CPTEC/INPE model data server — https://dataserver.cptec.inpe.br/dataserver_modelos/brams/
- CPTEC/INPE — https://www.cptec.inpe.br/
- gov.br service page (PNT) — https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
