# CMA-GFS (formerly GRAPES-GFS)

## What this model is
CMA-GFS is the operational global deterministic numerical weather prediction system developed and run by the China Meteorological Administration. It is the core global medium-range forecasting system of Chinese operational meteorology and provides initial and boundary conditions for CMA's downstream regional and specialty models.

The model was previously known as **GRAPES-GFS** (Global/Regional Assimilation and Prediction Enhanced System — Global Forecasting System). The system was renamed **CMA-GFS** in 2021. The legacy "GRAPES" name still appears in directory paths on CMA's public distribution endpoint and in older literature, but the current official name is CMA-GFS.

---

## Who runs it
- **Organization:** China Meteorological Administration (CMA) / CMA Earth System Modeling and Prediction Centre (CEMC)
- **Country / region:** China

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Regular latitude–longitude grid, C-grid horizontal staggering, Charney–Phillips vertical staggering
- **Distributed grid dimensions:** 2880 × 1440 = 4,147,200 points at 0.125° × 0.125° (live-verified from GRIB2 Section 3). Latitudes 89.9375° to −89.9375°, longitudes 0° to 359.875°. The grid is **cell-centred and pole-avoiding** — offset half a grid cell in latitude, so neither pole is a grid point. This is why the grid has 1440 rather than 1441 rows.

---

## Basic details
- **Model type:** Deterministic global NWP
- **Model system / core:** CMA-GFS dynamical core (descended from GRAPES); semi-implicit semi-Lagrangian (SISL) with a predictor–corrector time integration scheme
- **Dynamical formulation:** Non-hydrostatic, fully compressible, shallow-atmosphere approximation in spherical coordinates
- **Convection-allowing:** No (global medium-range resolution)
- **Horizontal resolution:** ~0.125° (~12.5 km) since V4.0; was ~0.25° (~25 km) in V3.x
- **Vertical levels:** 87
- **Model top:** ~0.1 hPa (~63 km)
- **Forecast length:**
  - 240 hours (10 days) for 00 and 12 UTC cycles
  - 120 hours (5 days) for 06 and 18 UTC cycles
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 3-hourly for all four cycles (live-verified)
  - Note: the 00/12 UTC distribution subdirectory is named `f0_f240_6h`, but contains 81 files at uniform 3-hour steps from 0 to 240 h. The `_6h` in the directory name does not reflect the actual output cadence.

---

## Data assimilation
- **Data assimilation:** Yes — global 4D-Var
- **Method / cadence:** Incremental 4D-Var (Courtier et al., 1994) with a 6-hour assimilation window, run 4× daily. In V4.0 the system adopted a **strong-constraint** framework, replacing the earlier weak-constraint approach that used a digital filter. The V4.0 incremental analysis uses outer-loop / inner-loop horizontal resolutions of 0.25° / 1.0° with model time steps of 450 s (outer) and 900 s (inner).
- **Radiative transfer models:** RTTOV (v12.x) and the Advanced Radiative transfer Modeling System (ARMS, developed by CMA). ARMS was integrated in V4.0.
- **Satellite data assimilation:** Satellite-derived observations account for roughly 80% of assimilated data, including from Chinese FY (Fengyun) satellites — e.g. AMSU-A, MHS, ATMS, MWTS, MWHS, IASI, HIRAS, and FY-4A GIIRS (hyperspectral IR) and AGRI. Assimilation of AMSU-A surface-sensitive channels over land and three-dimensional cloud detection for FY-4A GIIRS are recent additions.

---

## What it provides
Deterministic global forecasts of:
- Temperature, humidity, and wind (including near-surface wind at 10, 30, 50, 70, 100, 120, 140, 160, 180, and 200 m above ground)
- Surface and mean sea level pressure
- Precipitation (including precipitation phase/type, added in V4.0)
- Cloud and hydrometeor fields (cloud water, rain, ice, snow, graupel mixing ratios; layer cloud cover)
- Soil temperature and soil moisture on 4 layers (0–10, 10–40, 40–100, 100–200 cm)
- Stability indices: CAPE, CIN, K index, SWEAT, parcel/best lifted index, Showalter index
- Vertical wind shear on 1000 / 3000 / 6000 m layers; boundary layer height
- Visibility, ceiling, cloud base and cloud top; precipitable water; total column cloud water and cloud ice
- Surface and top-of-atmosphere radiation fluxes (all-sky and clear-sky)

**Upper-air level structure** (live-verified — 40 distinct isobaric levels, not a single uniform set):
- **Geopotential height, temperature, and u/v wind: all 40 levels** — 1000, 975, 950, 925, 900, 850, 800, 750, 700, 650, 600, 550, 500, 450, 400, 350, 300, 275, 250, 225, 200, 175, 150, 125, 100, 70, 50, 30, 20, 10, 7, 5, 4, 3, 2, 1.5, 1, 0.5, 0.2, 0.1 hPa
- **All other upper-air variables: the lowest 30 levels only** (1000 → 10 hPa) — specific and relative humidity, dew point, vertical velocity, divergence, vorticity, hydrometeor mixing ratios, layer cloud cover, pseudo-adiabatic potential temperature

---

## Data availability
- **Is the data free?** Yes
- **License:** Distributed through CMA's GISC/WMC Beijing node of the WMO Information System (WIS) as WMO "essential" data under the **WMO Unified Data Policy (Resolution 1, Cg-Ext-2021, successor to Resolution 40)**, which provides for free and unrestricted international exchange. No CC-style license is published for this specific product; users should follow WMO data-policy attribution conventions. (TBD — no product-specific open-data license located.)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, **JPEG 2000 packed** (`packingType = grid_jpeg`, 12–16 bits/value). Some GRIB tooling requires an explicit JPEG 2000 codec (e.g. OpenJPEG or Jasper) to decode these; plain simple-packing readers will fail.
- **Official download location:**
  http://data.wis.cma.cn/DCPC_WMC_BJ/open/nwp/gmf_gra/

Plain nginx directory listings over HTTP, no authentication or registration. `Accept-Ranges: bytes` is honoured, so byte-range requests can be used to pull individual GRIB messages instead of whole ~1.4 GiB files.

Data is organized as `<cycle>/<forecast-range>/<files>` — note the intermediate directory level:
- `t0000/f0_f240_6h/` — 00 UTC cycle, forecast hours 0–240, **3-hour steps, 81 files**
- `t0600/f0_f120_3h/` — 06 UTC cycle, forecast hours 0–120, 3-hour steps, 41 files
- `t1200/f0_f240_6h/` — 12 UTC cycle, forecast hours 0–240, **3-hour steps, 81 files**
- `t1800/f0_f120_3h/` — 18 UTC cycle, forecast hours 0–120, 3-hour steps, 41 files

**The `f0_f240_6h` directory name is misleading** — those directories contain 3-hourly, not 6-hourly, output (live-verified across multiple cycles).

**File naming:** WMO-style filenames of the form
`Z_NAFP_C_BABJ_<YYYYMMDDHHMMSS>_P_NWPC-GRAPES-GFS-GLB-<HHHMM>.grib2`
where `<YYYYMMDDHHMMSS>` is the cycle initialization time and `<HHHMM>` is the forecast step encoded as hours and minutes — so `02400` is 24 h and `24000` is 240 h, not 24 000 of anything. Example:
`Z_NAFP_C_BABJ_20260729180000_P_NWPC-GRAPES-GFS-GLB-02400.grib2`

**Volume and retention:** each step file is ~1.35–1.55 GiB and holds **861 GRIB2 messages** (inventory verified identical at f000 and f024). A 240 h cycle is ~111 GiB, a 120 h cycle ~56 GiB, giving **~335 GiB/day** for all four cycles. Retention on the open endpoint is a rolling window of roughly 2–3 days; there is no long-term archive here.

**Latency:** the first file of a cycle appears ~4.2–4.6 h after cycle time; a 120 h cycle finishes ~4.8 h after cycle time and a 240 h cycle ~5.6 h (measured from HTTP `Last-Modified` on the 2026-07-29 12 UTC and 18 UTC cycles).

The `gmf_gra` path component retains the legacy "GRAPES" naming despite the rebranding to CMA-GFS.

---

## Notes
- CMA-GFS is the deterministic counterpart to CMA's global ensemble system (CMA-GEPS), which also uses the GRAPES/CMA dynamical core and was described in earlier literature as GRAPES-GEPS. **The GEPS directories on this same endpoint (`open/nwp/cma_geps_glb/` and `open/nwp/gra_geps_glb/`) are dormant** — the directory trees exist but the leaf directories are empty, with last activity in January 2026 and 2022–23 respectively. This endpoint is not currently a working route to CMA-GEPS data. The neighbouring `open/sds_cuace_dust/`, `open/bcccsm-monthly-average/` and `open/bcccsm-seasonal-average/` directories are also empty.

### Decoding notes (live-verified)
- **Accumulated fields are run-totals, not per-interval buckets.** `stepRange` reads `0-3`, `0-6`, `0-24` and so on, for precipitation (convective, large-scale, total, snowfall) and all accumulated radiation and flux fields. Period values require differencing consecutive step files.
- **`tablesVersion = 4`** is encoded in Section 1, which is old enough that current eccodes returns `shortName = 'unknown'` for a number of parameters that are in fact standard WMO entries. Resolving `discipline` / `parameterCategory` / `parameterNumber` against WMO GRIB2 Code Table 4.2 identifies these as: total precipitation (0/1/8, APCP), total snowfall (0/1/29, ASNOW), dew point depression (0/0/7), total column integrated water vapour / cloud water / cloud ice (0/1/64, 0/1/69, 0/1/70), cloud base (0/6/11), CAPE (0/7/6), CIN (0/7/7), and Showalter index (0/7/13).
- **23 distinct local-use parameters** (parameter number ≥ 192) account for **198 of the 861 messages** in every file. These cannot be identified without CMA's local parameter table, which does not appear to be published. Among them, category 16 (forecast radar imagery) number 225 is present on all 30 levels and is most likely simulated reflectivity — **TBD, unconfirmed.**
- **The 1.5 hPa level is easily mistaken for a duplicate.** eccodes reports `level = 1` for both the 1.5 hPa and 1.0 hPa fields because the `level` key is integer hPa. Read `scaledValueOfFirstFixedSurface` (150 vs 100 Pa) to distinguish them. Separately, the three levels above 1 hPa (0.5, 0.2, 0.1 hPa) are encoded as `isobaricInPa`, so a filter on `typeOfLevel == 'isobaricInhPa'` silently drops them.
- Model output tops out at 0.1 hPa, consistent with the documented ~0.1 hPa model top. The **87 model levels** figure is a documentation value and is not verifiable from the distributed products, which are pressure-level only — no native-level (hybrid/height coordinate) fields are published on this endpoint.
- Distribution is via CMA's WIS public endpoint operated by the World Meteorological Centre Beijing. The `gmf_gra` directory path preserves the legacy GRAPES naming; documentation referring to "GRAPES-GFS" or "GFS GRAPES" describes the same system now officially called CMA-GFS.
- CMA-GFS provides initial/boundary conditions for CMA's national and regional limited-area systems (e.g. CMA-MESO) and specialty models (e.g. CMA-TYM for typhoons), and is a key data source for AI-based forecasting research at CMA.
- After more than 20 years of independent development, CMA reports the Northern Hemisphere predictable-days score exceeding 8 days for the first time with V4.0, narrowing the gap with ECMWF and NCEP.

---

## Recent version history

### CMA-GFS V4.0 — operational 22 May 2023
(Passed operational review 21 February 2023.)
- Horizontal resolution increased from 0.25° (~25 km) to 0.125° (~12.5 km)
- Northern Hemisphere predictable days exceeded 8 for the first time
- Precipitation phase/state products added
- **Cloud microphysics:** graupel-related microphysical processes added to the Liu-Ma scheme (graupel colliding with cloud water, ice crystals, and snow; auto-conversion of ice crystals and snow to graupel; graupel melting and sublimation); cloud/rain evaporation rate restricted to improve precipitation efficiency
- **Convection (NSAS scheme):** sub-cloud-layer environmental relative humidity added to the convection trigger over land; entrainment-rate sensitivity to environmental humidity increased; quasi-equilibrium closure optimized — reducing spurious light rain and missed heavy rain
- **Mass conservation:** a mass-conservation correction algorithm introduced to address long-integration mass loss and the associated decay of synoptic systems (e.g. the subtropical high)
- **Efficiency:** the 3D reference profile replaced by a **2D reference profile** (time step extended from 240 s to 300 s at 0.125°); the Helmholtz solver switched from GCR to a **preconditioned classical Stiefel iteration (PCSI)**; radiation and predictor–corrector algorithms optimized — overall integration efficiency increased by ~1/3
- **Radiative transfer / DA:** ARMS integrated alongside RTTOV; strong-constraint 4D-Var framework adopted

### GRAPES-GFS V3.1 — operational April 2021
- Methane oxidation scheme added (improves upper-stratosphere water vapor and reduces temperature/height bias)
- Updated land surface model (including supercooled soil water)
- Improved moist physical processes / cumulus convection (revised trigger, auto-conversion, sub-cloud treatment, shallow-convection entrainment/detrainment)

### CMA-GFS V3.0 — dynamical core upgrade, operational June 2020
(Described in Shen et al., 2023.)
- Classical 2TL SISL scheme extended to a predictor–corrector formulation (avoiding the temporal extrapolation of nonlinear residuals and midpoint winds that caused instability)
- 3D reference profile replaces the original isothermal reference profile
- Hybrid height-based terrain-following vertical coordinate introduced
- New spectral-filter terrain and reduced artificial damping
- Time integration now second-order accurate; time step extended by ~50%; ~30% efficiency improvement
- Combined with corresponding 4D-Var modifications, this constituted CMA-GFS V3.0 (replacing V2.4)

---

## Official documentation
- World Meteorological Centre Beijing: http://www.wmc-bj.net/
- CMA-GFS V4.0 operational announcement (CMA): https://www.cma.gov.cn/en2014/research/News/202306/t20230601_5545266.html
- CMA-GFS V4.0 operational review (CMA): https://www.cma.gov.cn/en2014/news/News/202303/t20230306_5344412.html
- GRAPES-GFS V3.1 upgrade (WMC Beijing): http://www.wmc-bj.net/publish/cms/view/40ee0aaf59874b0c821993238e7726b4.html
- CMA Earth System Modeling and Prediction Centre: https://www.cma.gov.cn/

### Key references
- Shen, X. S., Y. Su, H. L. Zhang, and J. L. Hu, 2023: New Version of the CMA-GFS Dynamical Core Based on the Predictor–Corrector Time Integration Scheme. *J. Meteor. Res.*, **37**(3), 273–285. doi:10.1007/s13351-023-3002-0
- Zhang, J., J. Sun, X. Shen, et al., 2023: Key Model Technologies of CMA-GFS V4.0 and Application to Operational Forecast. *J. Appl. Meteor. Sci.*, **34**(5), 513–526. doi:10.11898/1001-7313.20230501 (in Chinese)
- Zhang, L., et al., 2019: The operational global four-dimensional variational data assimilation system at the China Meteorological Administration. *Quart. J. Roy. Meteor. Soc.*, **145**, 1882–1896. doi:10.1002/qj.3533
- Xiao, H., et al., 2023: Assimilation of AMSU-A Surface-Sensitive Channels in CMA_GFS 4D-Var System over Land. *Wea. Forecasting*, **38**(9), 1777–1790. doi:10.1175/WAF-D-23-0032.1
- Wang, L., et al., 2025: Impact of a New Three-Dimensional Cloud Detection Method of FY4A GIIRS in the CMA-GFS. *Wea. Forecasting* (early online release). doi:10.1175/WAF-D-24-0087.1
