# ICON-ART-EU (European Mineral Dust Forecast)

## What this model is
ICON-ART-EU is the regional **mineral-dust** forecast of DWD's ICON-ART system: the large nested domain of the global [ICON-ART](../../global/germany/icon-art.md) run, covering **Europe, North Africa and the North Atlantic**. DWD calls this domain **ICON-ART-EU-NA²**. It predicts the emission, transport, sedimentation and dry/wet deposition of mineral dust (notably Saharan dust transported into Europe), and distributes a standard meteorological field set alongside the ART dust parameters.

> **Correction — this is not the ICON-EU nest.** Earlier versions of this entry described ICON-ART-EU as "ICON-EU extended with ART" at ~6.5 km. It runs on its own aerosol domain, which reaches from the equator to 82°N and is much larger than the operational ICON-EU nest, at **~10 km** (grid #61) since 6 October 2026 and ~13 km (grid #51) before. DWD's change notice of 6 October 2026 describes it as "a large nested domain covering Europe, North Africa and the Northern Atlantic (ICON-ART-EU-NA²)", and says the ICON-EU domain enlargement on that date did not affect it because it "already has a much larger nested domain".

The forecasts have been on the DWD Open Data Server since **April 2025**, under the `/weather/nwp/v1/m/` tier together with the global ICON-ART and both ART ensembles. The online-coupled design means dust and meteorology evolve together in one model run, with dust–radiation and dust–cloud (ice-nucleation) feedbacks acting on the forecast.

> **Note on scope / naming:** "ICON-ART-EU" is sometimes used loosely for DWD's ICON-ART-based *pollen* forecast as well. That is a **separate configuration on a separate distribution channel** — openly distributed as NetCDF under `/climate_environment/health/forecasts/pollen/`, not as GRIB in this feed. The `icon-art-eu` feed contains dust and standard meteorology only. See [ICON-ART Pollen](./icon-art-pollen.md).

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service; GRIB centre `edzw`, Offenbach), with the ART module developed and maintained by the Karlsruhe Institute of Technology (KIT)
- **Country:** Germany
- **On open data since:** April 2025

---

## What area it covers
- **Coverage:** Europe, North Africa and the North Atlantic (ICON-ART-EU-NA²)
- **Domain extent (cell centres, decoded from `CLAT`/`CLON`, 2026-10-18 12 UTC run):** **0.20°S – 82.18°N, 70.55°W – 70.55°E**. The southern edge reaches the equator, so the Saharan and Sahelian source regions lie inside the domain rather than at its boundary. Not changed by the 6 October 2026 upgrade beyond the finer cell spacing (grid #51 decoded as 0.27°S – 82.26°N, 70.62°W – 70.62°E).
- **Native grid (since 6 October 2026):** DWD grid **#61**, **1,013,404 cells**, ~10 km
  - Verified from live GRIB2 headers: `numberOfGridUsed = 61`, `numberOfDataPoints = 1013404`, `uuidOfHGrid = 7fe5661c7b47abbc0766699947a72c40`, `gridType = unstructured_grid`. DWD's 28 July 2026 newsletter gives 13 km → 10 km and 1.78× the grid points (1,013,404 / 571,088 = 1.77).
  - **Until 5 October 2026:** grid #51, 571,088 cells, ~13 km (R3B07 regional mesh, "bigger nest for prognostic aerosol forecasts with ICON-ART" in DWD's grid catalogue).
  - Grid #61 is not the same grid as the 10 km ICON-EU-EPS nest (grid #63, 342,428 cells) or deterministic ICON-EU (grid #62); their cells do not correspond.
- **Surface height (`HSURF`):** −410.28 m to 4,420.22 m

---

## Basic details
- **Model type:** Regional atmospheric composition / mineral-dust forecast (online-coupled)
- **Model system / core:** ICON + ART, run as the nested domain of the global ICON-ART system
- **Horizontal resolution:** ~10 km (grid #61) since 6 October 2026; ~13 km (grid #51) before
- **Vertical levels:** **74 full levels** (75 `HHL` half levels) — verified from the published level indices and the `HHL` header (`nlev = 75`). Unchanged by the October upgrade.
- **Forecast length:** +120 h at all four cycles
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC); all four cycles carry the same parameters and steps
- **Temporal output resolution:** parameter-dependent. Earlier versions of this entry gave "hourly to +72 h, then 3-hourly" for everything; only `TAOD_DUST` follows that. Most meteorological fields are hourly to +51 h only. See *Step schedules*.
- **Publication latency (v1, measured 2026-10-18/19):** first files ~2 h 37–40 min after cycle time; run complete ~3 h 03–11 min. Example: 2026-10-19 00 UTC run 02:36:39 → 03:11:47 UTC; 2026-10-18 06 UTC run 08:37:47 → 09:02:56 UTC. Starts at the same moment as the global ICON-ART.

---

## Meteorological driver
- **Driving NWP model:** Self — ICON-ART-EU is the nest of the global ICON-ART run, which supplies its lateral boundaries. No external driver.
- **Coupling:** Online — dust tracers use the same advection and subgrid transport operators as ICON's moisture fields; dust–radiation and dust–cloud feedbacks act on the meteorological forecast.
- **GRIB identity:** `generatingProcessIdentifier = 2` (the value used by ICON-EU), `typeOfProcessedData = fc`.

---

## Chemistry and aerosols
- **Composition species:** Mineral dust only (no gas-phase chemistry and no other aerosol species in this operational configuration).
- **Dust representation:** Three modes (A, B, C — fine, medium, coarse), carried as prognostic mass mixing ratios with companion `*0` fields.
- **ART processes (online):** Dust emission, advective/convective/turbulent transport, gravitational sedimentation, and dry and wet (convective + grid-scale) deposition.
- **Feedbacks:** Aerosol–radiation (dust attenuates shortwave radiation) and aerosol–cloud (dust as ice-nucleating particles).

---

## Emissions
- **Dust (natural):** Online emission computed by ART from the model's own near-surface meteorology and surface characteristics, against a threshold friction velocity. `USTAR` and `USTAR_THRES` are both published.
- **Anthropogenic / biogenic / other:** Not applicable.

---

## Data assimilation
- **Assimilates composition observations:** No dust or aerosol assimilation is documented; the dust field is free-running from the emission scheme. Meteorology inherits DWD's ICON analysis.

---

## What it provides
**132 parameter folders** (131 after `VIS` is removed after 30 November 2026): a meteorological set plus **28 ART mineral-dust parameters**.

### ART dust parameters
- **`TAOD_DUST`** — total-atmosphere optical depth due to mineral dust (dimensionless); the "dust optical depth" downstream sites typically use. GRIB `productDefinitionTemplateNumber = 48`.
- **`DUST_TOTAL_MC`** — total dust mass concentration on model levels (kg m⁻³)
- **`DUST_TOTAL_MC_VI`** — column total dust mass (kg m⁻²)
- **`DUST_MAX_TOTAL_MC_LAYER`** — maximum total dust mass concentration in each of 7 layers (published under `lvt1/100/` with non-standard level values)
- **`DUSTA` / `DUSTB` / `DUSTC`** (+ `DUSTA0` / `DUSTB0` / `DUSTC0`) — per-mode fields
- **`AER_DUST`** — layer dust optical depth (GRIB `shortName` decodes as `AOD_DUST`)
- **`CEIL_BSC_DUST`, `SAT_BSC_DUST`** — ceilometer- and satellite-geometry attenuated backscatter at 532 and 1064 nm
- **`ACCEMISS_DUST{A,B,C}`, `ACCDRYDEPO_DUST{A,B,C}`, `ACCSEDIM_DUST{A,B,C}`, `ACCWETDEPO_CON_DUST{A,B,C}`, `ACCWETDEPO_GSP_DUST{A,B,C}`** — accumulated emission, dry deposition, sedimentation and convective/grid-scale wet deposition per mode

### Meteorological set
Screen-level and 10 m fields, MSLP and surface pressure, cloud cover by layer and `CLDEPTH`, `CEILING`, `HBAS_CON`/`HTOP_CON`/`HTOP_DC`, `HZEROCL`, precipitation split by phase and convective/grid-scale, radiation terms, `CAPE_ML` and `CAPE_MU`, `LPI_CON_MAX`, column water species, lake, snow, sea-ice and soil fields, `DEN` (air density) and, until 30 November 2026, `VIS`.

**There is no pressure-level output in this feed** — no `FI`, `RELHUM`, `T` or wind on isobaric surfaces. The global [ICON-ART](../../global/germany/icon-art.md) feed has `FI`/`RELHUM` on 18 pressure levels; it also carries `AUMFL_S`, `AVMFL_S`, `CAPE_CON`, `NDVIRATIO`, `RUNOFF_G`, `RUNOFF_S`, `TQC_DIA`, `TQI_DIA` and `WW`, which this feed does not. Conversely `CAPE_MU`, `CEILING`, `DEN` and `VIS` are in this feed only.

### Vertical coverage is heavily subset

| Field group | Levels published |
|---|---|
| `DUST_TOTAL_MC`, `CEIL_BSC_DUST`, `SAT_BSC_DUST` | 44 model levels, **31–74** |
| `T`, `P`, `QV`, `QC`, `QI`, `QR`, `QS`, `U`, `V`, `DEN`, `AER_DUST`, `DUSTA/B/C`, `DUSTA0/B0/C0` | 3 model levels, **72–74** (near-surface) |
| `HHL`, `W` | 4 half levels, 72–75 |
| `T_SO` | 9 soil depths, 0–14.58 m |
| `W_SO`, `W_SO_ICE`, `SMI` | 8 soil layers, 0–7.29 m |

### Step schedules (identical in all four cycles; verified on the 2026-10-18 06 and 12 UTC runs)

| Steps | Schedule | Parameters |
|---|---|---|
| 89 | Hourly to +72 h, then 3-hourly to +120 h | `TAOD_DUST` |
| 77 | Hourly to +72 h, then 12-hourly to +120 h | `DUSTA/B/C`, `DUSTA0/B0/C0` |
| 75 | Hourly to +51 h, then 3-hourly to +120 h | 19 fields: `T_2M`, `T_2M_L`, `TD_2M`, `TD_2M_L`, `U_10M`, `V_10M`, `PMSL`, `TOT_PREC`, `RAIN_CON`, `RAIN_GSP`, `SNOW_CON`, `SNOW_GSP`, `CLCL`, `CLCT`, `ASOB_S`, `ASWDIR_S`, `ASWDIFD_S`, `LPI_CON_MAX`, `W_SNOW` |
| 64 | Hourly to +51 h, +54 h, then 6-hourly to +120 h | 60 fields, incl. `VMAX_10M`, `VIS`, `CEILING`, `CAPE_ML`, `CAPE_MU`, `CLCH`, `CLCM`, `PS`, `RELHUM_2M`, `TMAX_2M`, `TMIN_2M`, column water, model-level `T/U/V/P/Q*`, `W`, soil, lake, snow and ice fields |
| 41 | 3-hourly, 0–120 h | All 15 accumulated budget terms, `DUST_TOTAL_MC`, `DUST_TOTAL_MC_VI`, `DUST_MAX_TOTAL_MC_LAYER`, `USTAR`, `USTAR_THRES` |
| 15 | 6-hourly to +48 h, then 12-hourly to +120 h | `AER_DUST`, `CEIL_BSC_DUST`, `SAT_BSC_DUST`, `DEN` |
| 1 | Step 0 only | `CLAT`, `CLON`, `ELAT`, `ELON`, `HHL`, `HSURF`, `FR_LAND`, `FR_LAKE`, `DEPTH_LK`, `SOILTYP`, `SSO_STDH`, `Z0`, `LAI`, `PLCOV`, `ROOTDP`, `EVAP_PL`, `ALB_SEAICE`, `HSNOW_MAX`, `SNOAG`, `SMI` |

Note that `VMAX_10M` here is not hourly beyond +51 h, unlike the global ICON-ART and both ART ensembles, which publish it every hour of the run.

---

## Data availability
- **Is the data free?** Yes
- **Licence:** **CC BY 4.0**, attribution required (DWD legal notice; EU High Value Dataset)
- **Is the data downloadable?** Yes — plain HTTPS, no registration
- **Data format:** GRIB2, one message per file, **no `.bz2` wrapper**; GRIB-internal CCSDS packing (`grid_ccsds`). Native triangular grid only.
- **Primary access:** https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eu/
- **Path structure:**
  - `…/icon-art-eu/p/{PARAM}/r/{YYYY-MM-DDTHH%3A00}/s/PT{hhh}H{mm}M.grib2` — single-level fields
  - `…/icon-art-eu/p/{PARAM}/lvt1/{levelType}/lv1/{level}/r/{run}/s/PT{hhh}H{mm}M.grib2` — multi-level fields (`150` model level, `106` soil depth in m, `100` for the dust-layer maximum)
  - `…/icon-art-eu/p/{PARAM}/wvl1/{nm}/lvt1/150/lv1/{level}/r/{run}/s/PT{hhh}H{mm}M.grib2` — `CEIL_BSC_DUST`, `SAT_BSC_DUST`
  - Example: `https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eu/p/TAOD_DUST/r/2026-10-18T12%3A00/s/PT024H00M.grib2`
- **Run volume (server manifest):** ~12.4 GB / ~15,100 files per run, all four cycles
- **Retention (observed 2026-10-19):** about 24 hours — the four most recent runs, each with its **complete** step list. Earlier versions of this entry said elapsed lead times are pruned as a run ages; that does not happen.
- **Ensemble companion:** [`icon-art-eu-eps`](./icon-art-eu-eps.md)

### Grid files and regridding
- **Lightest route:** the `CLAT`/`CLON` and `ELAT`/`ELON` folders give cell-centre and vertex coordinates as GRIB fields at step 0. They changed with the grid on 6 October 2026.
- **Grid definition files:** https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M) — grid number **61** for current data, **51** for data before 6 October 2026.
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid (cdo-based).
- `/weather/lib/cdo/` never held a file for either ART regional grid, and it is deleted after 30 November 2026.

---

## Notes
- **`VIS` is removed from this feed after 30 November 2026.** DWD's 2 September 2026 newsletter names `https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eu/p/VIS/` explicitly, following the assessment of DWD's aviation weather service. Visibility is withdrawn from all ICON open data on that date. Until then `VIS` is published on the 64-step schedule.
- **The 6 October 2026 grid change splits any archive.** Grid #51 (13 km, 571,088 cells) before, grid #61 (10 km, 1,013,404 cells) after. Check `numberOfGridUsed` before stacking data across that date.
- **Pollen product (separate entry):** DWD runs ICON-ART in limited-area mode for a European pollen forecast — hazel, alder, birch, grasses, ragweed — operational since September 2021 and co-developed with GeoSphere Austria, MeteoSwiss and KIT. The Germany subset is openly distributed as daily-mean NetCDF at `https://opendata.dwd.de/climate_environment/health/forecasts/pollen/`. It is catalogued as [ICON-ART Pollen](./icon-art-pollen.md).
- **Not "dust only" in file terms:** the feed carries a full near-surface meteorological set in addition to dust.
- **Folder name ≠ GRIB shortName for `AER_DUST`** (decodes as `AOD_DUST`).

---

## Relationship to other models
- **[ICON-ART](../../global/germany/icon-art.md):** the global parent run (~20 km, grid #39), which supplies this nest's boundaries.
- **[ICON-ART-EU-EPS](./icon-art-eu-eps.md):** the 10-member ensemble companion on the same grid #61 and the same 74 levels.
- **[ICON-EU](../../../nwp_models/regional/germany/icon-eu.md):** DWD's deterministic European NWP nest — a different, smaller domain (grid #62, ~6.5 km). Not the same model or grid.
- **[ICON-ART Pollen](./icon-art-pollen.md):** the separate limited-area pollen configuration.

---

## Recent version history

### After 30 November 2026 — `VIS` removed, `/weather/lib/cdo/` deleted
Announced in DWD's Open Data newsletter of 2 September 2026. ICON-ART-EU is already on the v1 URL scheme, so the shutdown of the legacy ICON trees on that date does not otherwise affect it.

### 6 October 2026 — resolution increase (effective 06 UTC run)
Per DWD's change notice of 6 October 2026 and the 28 July 2026 newsletter: the ICON-ART-EU-NA² nest refined from 13 km to 10 km (grid #51 → grid #61, 1.78× the grid points), with the global ICON-ART domain going from 26 km to 20 km. Vertical resolution unchanged. The domain is unchanged; the ICON-EU enlargement made on the same day did not apply to ICON-ART. `CLAT`/`CLON`/`ELAT`/`ELON` changed with the grid. Test data for the 2026-07-15 00 UTC run were offered beforehand under `https://opendata.dwd.de/test/weather/nwp/v1/m/icon-art-eu/`.

### 16 June 2026 — CCSDS packing
DWD switched ICON-family GRIB2 output to CCSDS packing, visible on this tier as the `grid_ccsds` packing type.

### April 2025 — ICON-ART on DWD Open Data
DWD published the ICON-ART system (global ICON-ART and regional ICON-ART-EU, plus the ensemble counterparts) under `/weather/nwp/v1/m/`, adding the ART mineral-dust parameters to the standard parameter set.

---

## Official documentation
- DWD Open Data Server, ICON-ART-EU: https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eu/
- DWD Open Data newsletter, ICON-ART launch (8 April 2025): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_april2025_1.html
- DWD Open Data newsletter, October resolution change (28 July 2026): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_juli2026_2.html
- DWD Open Data newsletter, new URL scheme / VIS / lib/cdo (2 September 2026): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD ICON-EPS / ICON-ART resolution upgrade notice (6 October 2026, `pdf_icon_eps_06_10_2026.pdf`): on DWD's ICON-EPS model-change notices page
- DWD ICON-ART / COSMO-ART overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/03_environmental_forecasts/icon_art_cosmo_art_en.html
- ICON model / ART parameter documentation: https://docs.icon-model.org/atmosphere/art/art.html
- KIT ICON-ART: https://www.icon-art.kit.edu/
- DWD ICON grids at MPI-M: https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- Regridding guide: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html

### Key references
- Rieger, D., et al. (2015). ICON-ART 1.0 — a new online-coupled model system from the global to regional scale. *Geosci. Model Dev.*, 8, 1659–1676.
- Hoshyaripour, G. A., et al. (2026). The atmospheric composition component of the ICON modeling framework: ICON-ART version 2025.10. *Geosci. Model Dev.*, 19, 1645–1681.

---

*Live verification performed 2026-10-18/19 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eu/` (2026-10-18 06/12/18 UTC and 2026-10-19 00 UTC runs): all 132 parameter folders enumerated, step schedules and level sets taken from the server manifest (`content.log.bz2`), GRIB2 headers and `CLAT`/`CLON` extents decoded with ecCodes.*
