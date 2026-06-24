# BESM (Brazilian Earth System Model)

## What this model is
BESM is CPTEC/INPE's coupled atmosphere–ocean–sea-ice Earth System Model, run as an operational long-range prediction system. Each daily run produces a coupled forecast extending roughly 13 months ahead, placing it in the seasonal-to-interannual class. The atmospheric component derives from CPTEC's AGCM (the same lineage as [BAM](../../../nwp_models/global/brazil/bam-cptec.md)) and the ocean component from the GFDL MOM family. Output is split into an atmospheric stream (GrADS binary) and an ocean stream (NetCDF).

Note that BESM here is an *initialized, calendar-dated seasonal-to-interannual forecast* issued on a continuous daily cycle — not a climate-projection (scenario) model.

---

## Who runs it
- **Organization:** CPTEC/INPE (Center for Weather Forecasting and Climate Studies / National Institute for Space Research)
- **Country / region:** Brazil
- **Coordinating body / programme (optional):** TBD

---

## What area it covers
- **Coverage:** Global (coupled Earth-system model)
- **Domain details:** The native model is global. The **distributed atmospheric** GrADS fields are post-processed onto a ~1.875° regular lat-lon grid (110 × 65) spanning roughly **150°W–54°E, 70°S–50°N** (an Americas–Atlantic window, not the full globe). The **ocean** NetCDF fields are global.

---

## Basic details
- **Model type:** Long-range coupled forecast system — deterministic (with at least two atmospheric streams; see Notes)
- **Model system / core:** BESM (Brazilian Earth System Model), run here as a coupled GCM (`CGCM`)
- **Range class:** Seasonal to interannual (out to ~13 months)
- **Forecast length:** ~395 days (~13 months); init + ~9480 h
- **Initialization cadence:** Daily (00 UTC)
- **Ensemble generation:** TBD — two labeled atmospheric streams (`ETA`, `NMC`) are present per run; whether these constitute a small ensemble or paired control/streams is unconfirmed
- **Ensemble size:** TBD
- **Temporal output resolution:** Atmosphere 6-hourly; ocean 3-hourly (e.g., SST) with additional monthly ocean fields
- **Output aggregation levels:** 6-hourly atmospheric fields (38 pressure levels); 3-hourly and monthly ocean fields

---

## Coupled configuration
- **Atmosphere:** CPTEC AGCM (BAM lineage), spectral **T62** (~1.875°), **42** native levels; distributed output on 38 pressure levels (1000–1 hPa)
- **Ocean:** GFDL MOM family (BESM's ocean component); resolution and levels TBD
- **Sea ice:** Included (ocean stream provides a sea-ice field); model/version TBD
- **Land surface (optional):** TBD
- **Coupling notes (optional):** Fully coupled atmosphere–ocean–sea-ice; coupling details TBD

---

## Initialization
- **Atmosphere IC:** TBD — the two streams (`ETA`, `NMC`) may reflect different initial-condition sources; unconfirmed
- **Ocean IC:** TBD (an ocean analysis/reanalysis)
- **Sea ice / land IC (optional):** TBD
- **Perturbation method (optional):** TBD

---

## Hindcasts (reforecasts)
- **Hindcast period:** TBD
- **Hindcast cadence / ensemble size:** TBD
- **Reference climatology period:** TBD
- **Distributed alongside forecasts?** TBD (the public feed shows real-time forecast runs; a hindcast set is not evident in this directory tree)

---

## What it provides

### Atmosphere (GrADS, 38 pressure levels; 17 variables)
Confirmed fields include zonal and meridional wind (UVEL, VVEL), absolute temperature (TEMP), geopotential height (ZGEO), surface pressure (PSLC), sea-level pressure (PSNM), topography (TOPO), and land–sea mask (LSMK), plus additional fields to make 17 total (enumerable from the `.ctl` descriptors).

### Ocean (NetCDF)
- Sea surface temperature (`besm_tos`)
- Temperature and salinity (`tempsalt`)
- Zonal and meridional currents (`ucur`, `vcur`)
- Sea ice (`ice`)
- Surface fluxes (`srflx`)
- Monthly ocean fields (`ocean_month`)
- Static grid / bathymetry (`ocean_static`)

---

## Data availability
- **Is the data free?** Yes — free of charge, no registration.
- **License:** **Transitional / not yet an open license.** Data is freely accessible and usable personally today, but CPTEC/INPE's operational-server notice restricts commercial use and redistribution in published/dissemination outlets without express CPTEC/INPE authorization, and requires attribution to "CPTEC/INPE." INPE's Open Data Plan (PDA 2025–2027, Decreto 8.777/2016) commits to publishing its global climate-prediction data as open data on dados.gov.br — the closest scheduled item is "Clima Global" (~June 2027), which may or may not correspond to this BESM feed (the PDA does not name BESM explicitly; mapping unconfirmed). As of late June 2026 nothing in this family is yet live on the dados.gov.br "Tempo e Clima" category; open reuse terms apply once it appears there.
- **Is the data downloadable?** Yes
- **Data formats:** Mixed — **GrADS binary** for the atmosphere (a data file tagged `.TQ0062L042` with no conventional extension, plus a `.ctl` descriptor per field/timestep; also `.lst` index lists), and **NetCDF** for the ocean (`.nc`, with companion GrADS `.ddf` descriptors). The atmospheric GrADS stream needs GrADS-compatible tooling; the ocean NetCDF is broadly readable.
- **Official download location:**
  https://dataserver.cptec.inpe.br/dataserver_modelos/besm/
  - `brutos/<YYYY>/<MM>/<DD>/00/CGCM/ATMOS/TQ0062L042/` — atmospheric GrADS files
  - `brutos/<YYYY>/<MM>/<DD>/00/CGCM/OCEAN/` — ocean NetCDF files

---

## Sources of predictability (optional)
TBD — as a coupled seasonal-to-interannual system, skill is expected to derive substantially from ocean initial state (e.g., ENSO), but this is not documented in the public feed.

---

## Notes
- **Unusually frequent initialization:** Each daily 00 UTC run launches a full ~13-month coupled forecast. This is a notably high cadence for an interannual-range coupled system (most seasonal systems initialize monthly); flag for confirmation of whether this reflects a continuously cycled or experimental configuration.
- **`ETA` / `NMC` labels:** These are stream codes in CPTEC's GrADS post-processing filenames (`GPOS<stream>...`), **not** the Eta NWP model. The `ETA` stream carries the full 6-hourly forecast set; `NMC` is a smaller set. Their exact roles (e.g., differing initial-condition sources) are TBD.
- **Atmosphere window vs global ocean:** The distributed atmospheric GrADS grid is a sub-global Americas–Atlantic window (~150°W–54°E, 70°S–50°N), while the ocean NetCDF is global — confirm whether a global atmospheric product is distributed elsewhere.
- **Relationship to siblings:** BESM's atmospheric component shares lineage with [BAM](../../../nwp_models/global/brazil/bam-cptec.md). It may also relate to the PDA's "Clima Global" seasonal item (mapping unconfirmed).
- **Older data:** Only recent runs are served operationally; older data requires a request to CPTEC and is subject to availability.

---

## Official documentation
- CPTEC/INPE model data server — https://dataserver.cptec.inpe.br/dataserver_modelos/besm/
- CPTEC/INPE — https://www.cptec.inpe.br/
- gov.br service page (PNT) — https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
