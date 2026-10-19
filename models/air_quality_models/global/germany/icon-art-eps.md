# ICON-ART-EPS (Global Mineral Dust Ensemble)

## What this model is
ICON-ART-EPS is the 10-member ensemble configuration of DWD's global [ICON-ART](./icon-art.md) mineral-dust forecast. It runs the same ICON dynamical core with the same online-coupled ART (Aerosols and Reactive Trace gases) module on the same ~20 km icosahedral grid as its deterministic sibling (~26 km until 6 October 2026), and provides probabilistic guidance on mineral-dust emission, transport, sedimentation, and deposition — chiefly Saharan dust transported toward Europe — alongside a standard set of meteorological fields.

It was published on the DWD Open Data Server in **April 2025** together with the deterministic ICON-ART, the regional ICON-ART-EU, and ICON-ART-EU-EPS, on the `/weather/nwp/v1/m/` tier that uses DWD's new URL scheme.

> **The dust ensemble is effectively a twice-daily product.** All four daily cycles are published, but **the 06 and 18 UTC cycles carry no ART parameters at all** — they are meteorology-only, 6-hourly, and truncated at +120 h. Every dust field appears at 00 and 12 UTC only. See *Ensemble configuration* and *Notes*.

> **This entry is filed under `air_quality_models/` rather than `ensemble_models/`**, following the repository's convention that phenomenon ensembles use their phenomenon template. It uses `air-quality-model.template.md` with the optional **Ensemble configuration** section retained. Cross-linked in both directions with [ICON-ART](./icon-art.md).

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service; GRIB centre `edzw`, Offenbach), with the ART module developed and maintained by the Karlsruhe Institute of Technology (KIT)
- **Country:** Germany
- **On open data since:** April 2025

---

## What area it covers
- **Coverage:** Global
- **Native grid (since 6 October 2026, verified):** DWD grid **#39**, unstructured triangular mesh — **1,310,720 cells**, `numberOfGridUsed = 39`, `uuidOfHGrid = 49c340e2248d11e58105c12d5e3b00a4`, `gridDefinitionTemplateNumber = 101`, `gridType = unstructured_grid`. ~20 km nominal spacing. Identical to the deterministic [ICON-ART](./icon-art.md) and to global [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md), which moved to the same grid on the same day.
  - **Until 5 October 2026:** R3B06 grid #36, 737,280 cells, ~26 km. Data on the two grids cannot be combined cell by cell.
- **Georeferencing:** GRIB2 messages carry **no lat/lon**. Use the `CLAT`/`CLON` (cell centres) and `ELAT`/`ELON` (vertices) folders, which DWD publishes as GRIB fields at step 0 — they changed with the grid on 6 October, so re-download them — or fetch the grid definition for grid number **39** from MPI-M (https://icon-downloads.mpimet.mpg.de/dwd_grids.xml). DWD's regridding guide is at https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid. The old `/weather/lib/cdo/` directory never held a grid #39 file and is deleted after 30 November 2026.

---

## Basic details
- **Model type:** Air quality / atmospheric composition (ensemble)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) + ART module, online-coupled — same core, grid, vertical structure, and physics as the deterministic ICON-ART
- **Coupling:** Online — dust and meteorology evolve together within a single integration, with dust–radiation and dust–cloud feedbacks acting on the forecast
- **Horizontal resolution:** ~20 km (grid #39) since 6 October 2026; ~26 km (grid #36) before. **Not coarsened relative to the deterministic sibling** — DWD's change notice states that for ICON-ART "the deterministic forecast and the ensemble forecasts (10 additional members) are conducted with the same resolution". Worth noting when comparing against ICON/ICON-EPS, where the ensemble *is* coarser than the deterministic run.
- **Vertical levels:** 120 model levels (verified from the published level indices and the `HHL` header, `nlev = 121`). Unchanged by the October grid switch. Only small subsets are distributed — see *What it provides*.
- **Forecast length (verified):**
  - **00 / 12 UTC:** +180 h
  - **06 / 18 UTC:** +120 h, meteorology only
- **Update frequency / cycles:** 4× daily published (00, 06, 12, 18 UTC); **dust parameters at 00 and 12 UTC only**
- **Temporal output resolution:** Parameter-dependent, with ten distinct step schedules in the 00/12 cycles — see *What it provides*. The 06/18 cycles are uniformly 6-hourly.
- **Observed publication latency (v1, measured 2026-10-18/19):** first files ~2 h 37–41 min after cycle time; run complete ~3 h 13 min (06/18 UTC) to ~3 h 17–21 min (00/12 UTC). Example: 2026-10-19 00 UTC run 02:37:05 → 03:17:17 UTC; 2026-10-18 18 UTC run 20:38:48 → 21:13:52 UTC.

---

## Meteorological driver
- **Driving NWP model:** Self — ICON-ART-EPS *is* ICON with ART, run as an ensemble. There is no external meteorological driver.
- **Coupling:** Online (two-way)
- **Initial and boundary conditions:** TBD. DWD does not document how the ICON-ART-EPS members are initialized or whether they inherit initial conditions from the 40-member global [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md) LETKF analysis. The shared grid (#36 until October, #39 since) is suggestive but not evidence — see *Ensemble configuration*.

---

## Chemistry and aerosols
The ART configuration matches the deterministic [ICON-ART](./icon-art.md); see that entry for the full description. Summarised here:

- **Scope:** Mineral dust only. This is not a full air quality model — no ozone, no NOₓ, no PM speciation, no gas-phase chemistry in the operational configuration.
- **Aerosol treatment:** Modal — three log-normal dust modes, distributed as `DUSTA` / `DUSTB` / `DUSTC` (prognostic mass mixing ratios) and `DUSTA0` / `DUSTB0` / `DUSTC0`. Verified GRIB names identify these as fine, medium, and coarse modes (`DUSTA` decodes as *"Modal prognostic mass mixing ratio of mineral dust particles (fine mode)"*).
- **Gas-phase chemical mechanism:** Not applicable to this configuration.
- **Optical properties:** Dust optical depth is diagnosed and distributed both column-integrated (`TAOD_DUST`) and per model layer (folder `AER_DUST` — note the GRIB `shortName` is **`AOD_DUST`**, *"Diagnostic mineral dust optical depth"*, which does not match the directory name).
- **Lidar-space diagnostics:** Attenuated backscatter is produced for two wavelengths, 532 nm and 1064 nm, in both ground-looking (`CEIL_BSC_DUST`, ceilometer geometry) and space-looking (`SAT_BSC_DUST`, satellite geometry) forms. These are the only parameters on this feed with a wavelength dimension in the URL path.

---

## Emissions
- **Dust scheme:** Online — dust emission is computed within the run from the model's own surface state. Emission is diagnosed against a threshold friction velocity; both `USTAR` (frictional velocity) and `USTAR_THRES` (threshold friction velocity) are distributed, which makes the emission trigger inspectable directly from the public data.
- **Accumulated dust budget terms:** Per-mode accumulations of emission, dry deposition, wet deposition (convective and grid-scale, separately), and sedimentation — 15 parameters in total.
- **Anthropogenic / biogenic / wildfire emissions:** Not applicable — no species other than mineral dust are carried.
- **Whether emission parameters are perturbed across members:** TBD (see *Ensemble configuration*).

---

## Data assimilation
- **Assimilates AQ observations:** No dust or aerosol data assimilation is documented for ICON-ART or ICON-ART-EPS. The dust field is free-running from the emission scheme.
- **Meteorological assimilation:** TBD — depends on the unconfirmed initialization relationship with ICON-EPS.

---

## Ensemble configuration

- **Ensemble type:** Perturbation-based — a single model configuration run as multiple perturbed members, not a multi-model ensemble.

- **Ensemble size:** **10 members, no control.** Verified from the feed (`e/01` … `e/10`) and confirmed by DWD's April 2025 announcement, which states that both ART ensembles consist of 10 ensemble runs. GRIB `perturbationNumber` runs 1–10 and matches the directory number exactly; `numberOfForecastsInEnsemble = 10`, consistent with the member count actually published. No `e/00` directory and no member with `perturbationNumber = 0` exists — consumers expecting a control/perturbed split (common when ingesting ECMWF ENS or GEFS) must adapt. This matches the wider DWD pattern: none of ICON-EPS, ICON-EU-EPS, or ICON-D2-EPS publishes a control either.

- **Source(s) of perturbations:** **TBD — not documented by DWD.** Members are demonstrably distinct (RMS difference of 0.026 in `TAOD_DUST` between members 01 and 02 at +12 h, against a field mean of ~0.05), so meaningful spread exists, but neither the perturbation method nor the relationship to the 40-member global [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md) is stated anywhere in the announcement, the ICON documentation, or the GRIB headers. Specifically unresolved: whether the 10 members are a subset of, or independently generated from, the ICON-EPS ensemble; whether member *n* corresponds to ICON-EPS member *n*; and whether the ART side (dust emission, deposition, or optical parameters) is perturbed at all, or whether all spread propagates from meteorological perturbations through the emission scheme. Worth raising with DWD.

- **Chemistry / emissions differences vs deterministic sibling:** None identified. The ART module, mode structure, and emission scheme appear identical; the differences are in what is published, not how the model is configured.

- **Resolution, species, and output differences vs deterministic sibling:** Same grid, same resolution, same vertical structure, same 180 h range at 00/12 UTC. The published **parameter set is substantially thinner**: 97 folders against the deterministic model's 139.
  - **45 parameters are deterministic-only**, of which one is an ART field — **`DUST_MAX_TOTAL_MC_LAYER`** (layer-maximum total dust mass concentration) is not in the ensemble. The other 44 are meteorological: lake-model fields (`FR_LAKE`, `DEPTH_LK`, `H_ML_LK`, `C_T_LK`, `T_BOT_LK`, `T_MNW_LK`, `T_WML_LK`), surface/vegetation fields (`LAI`, `PLCOV`, `ROOTDP`, `RSTOM`, `SOILTYP`, `NDVIRATIO`, `EVAP_PL`, `W_I`), snow and ice detail (`FRESHSNW`, `RHO_SNOW`, `SNOAG`, `HSNOW_MAX`, `T_ICE`, `ALB_SEAICE`, `W_SO_ICE`), hydrology (`RUNOFF_G`, `RUNOFF_S`, `SMI`), additional hydrometeors (`QR`, `QS`, `TQR`, `TQS`, `QV_S`), diagnostics (`ALB_RAD`, `CAPE_CON`, `CLDEPTH`, `HTOP_DC`, `HZEROCL`, `WW`, `W`, `TCH`, `TCM`, `TQC_DIA`, `TQI_DIA`), momentum fluxes (`AUMFL_S`, `AVMFL_S`), and `ALHFL_BS`.
  - **3 parameters are ensemble-only:** `OMEGA` (500 hPa only), `SOBS_RAD`, and `THBS_RAD`.
  - **Cycle coverage is the larger difference.** The deterministic model publishes dust in all four cycles (109 steps at 00/12, 89 at 06/18); the ensemble publishes dust at 00/12 only.

- **Member packaging:** **One file per member**, as a directory segment `…/r/{run}/e/{NN}/s/{step}.grib2` — not a member dimension inside a combined file, and not the packed multi-member files used on the older `/weather/nwp/` tier. DWD lists robust handling of late or failed members as an explicit design goal of this URL scheme, and the consequence is that **a missing member is simply an absent directory**. Enumerate `e/` rather than assuming ten members are present. Ensemble encoding: `productDefinitionTemplateNumber` 1 / 41 / 49 / 58 / 68 depending on parameter class, `typeOfProcessedData = cp`, and `typeOfEnsembleForecast = 192` — a value in the 192–254 range reserved for local use, so it cannot be interpreted against the WMO code table and cannot be used to discriminate control from perturbed members.

- **Derived products distributed:** **None.** No ensemble mean, spread, quantile, or exceedance-probability fields are published — only raw members. Any probabilistic dust product must be computed by the user after fetching all ten members.

---

## What it provides

**27 ART mineral-dust parameters** plus a standard meteorological set, all on the native triangular grid.

### ART dust parameters

| Group | Parameters | Notes |
|---|---|---|
| Optical depth | `TAOD_DUST` | Column-integrated, dimensionless (`Numeric`) |
| Optical depth (layered) | `AER_DUST` | GRIB `shortName` is `AOD_DUST`; per model layer |
| Mass concentration | `DUST_TOTAL_MC`, `DUST_TOTAL_MC_VI` | kg m⁻³ (3D) and kg m⁻² (column) |
| Prognostic modes | `DUSTA`, `DUSTB`, `DUSTC` | kg kg⁻¹ mass mixing ratio, fine / medium / coarse |
| Mode number/aux | `DUSTA0`, `DUSTB0`, `DUSTC0` | |
| Accumulated emission | `ACCEMISS_DUST{A,B,C}` | kg m⁻², `stepType = accum` |
| Accumulated dry deposition | `ACCDRYDEPO_DUST{A,B,C}` | kg m⁻², accumulated |
| Accumulated wet deposition | `ACCWETDEPO_CON_DUST{A,B,C}`, `ACCWETDEPO_GSP_DUST{A,B,C}` | Convective and grid-scale, separately |
| Accumulated sedimentation | `ACCSEDIM_DUST{A,B,C}` | kg m⁻², accumulated |
| Lidar backscatter | `CEIL_BSC_DUST`, `SAT_BSC_DUST` | m⁻¹ sr⁻¹, at 532 nm and 1064 nm |
| Emission diagnostics | `USTAR`, `USTAR_THRES` | m s⁻¹ — friction velocity and its emission threshold |

Meteorological output covers the usual near-surface and screen-level set (2 m temperature and dewpoint, 10 m wind and gusts, MSLP, cloud cover by layer, precipitation split into rain/snow and convective/grid-scale, radiation budget terms, soil temperature and moisture, CAPE, total column water species).

### Vertical coverage is heavily subset

Only a fraction of the 120 model levels is published, and the subset differs sharply between dust and meteorology:

| Field group | Levels published |
|---|---|
| `DUST_TOTAL_MC`, `CEIL_BSC_DUST`, `SAT_BSC_DUST` | **44 model levels, 77–120** — a genuine dust column |
| `T`, `QV`, `QC`, `QI`, `P`, `U`, `V`, `AER_DUST`, `DUSTA`/`B`/`C`, `DUSTA0`/`B0`/`C0` | **3 model levels only, 118–120** (near-surface) |
| `HHL` | 4 half levels, 118–121 |
| `FI`, `RELHUM` | 19 pressure levels: 100, 200, 500, 1000, 3000, 5000, 7000, 10000, 20000, 25000, 30000, 40000, 50000, 70000, 85000, 90000, 92500, 95000, 100000 Pa |
| `OMEGA` | 50000 Pa only |
| `W_SO` | 8 soil depths: 0.0, 0.01, 0.03, 0.09, 0.27, 0.81, 2.43, 7.29 m |
| `T_SO` | 9 soil depths: 0.0, 0.005, 0.02, 0.06, 0.18, 0.54, 1.62, 4.86, 14.58 m |

> **The prognostic dust modes are surface-only in practice.** `DUSTA`/`B`/`C` — the actual prognostic variables — are published on just three near-surface levels, while the *diagnostic* `DUST_TOTAL_MC` gets 44 levels. Vertical dust structure is therefore available only as total mass concentration and backscatter, not resolved by mode.

### Step schedules (00/12 UTC cycles, re-verified on the 2026-10-18 12 UTC run)

Ten distinct schedules coexist in a single run — unchanged by the October grid switch except that `FI` and `RELHUM` were found to follow three different schedules depending on pressure level (earlier versions of this entry gave 22 steps for all levels). Assuming a uniform time axis across parameters, or across levels of one parameter, will silently drop data.

| Steps | Schedule | Parameters |
|---|---|---|
| 180 | Hourly, 1–180 h | `VMAX_10M` |
| 111 | Hourly to 75 h, then 3-hourly to 180 h | 34 params incl. `T_2M`, `TOT_PREC`, `PMSL`, `CLCT`, `HTOP_CON`, `W_SO`, `OMEGA`; plus `FI` and `RELHUM` on the 300–1000 hPa levels (250 hPa excluded) |
| 109 | Hourly to 72 h, then 3-hourly to 180 h | `TAOD_DUST` |
| 61 | 3-hourly, 0–180 h | 19 params — all 15 dust accumulations, plus `DUST_TOTAL_MC`, `DUST_TOTAL_MC_VI`, `USTAR`, `USTAR_THRES` |
| 60 | 3-hourly, 3–180 h (no step 0) | `ASWDIFD_S`, `ASWDIR_S` |
| 49 | Hourly to 48 h | `LPI_CON_MAX` |
| 31 | 6-hourly, 0–180 h | 11 params incl. `T`, `QV`, `P`, `T_SO`, `CLCT_MOD`, `PS`, `RELHUM_2M`; also `FI`/`RELHUM` at 250 hPa |
| 30 | 6-hourly, 6–180 h (no step 0) | `TMAX_2M`, `TMIN_2M`, `ALHFL_S`, `ASHFL_S`, `ASWDIFU_S` |
| 22 | 6-hourly to 72 h, then 12-hourly to 180 h | `FR_ICE`, `H_ICE`, `H_SNOW`, `T_SNOW`; also `FI`/`RELHUM` on the 1–200 hPa levels |
| 20 | 6-hourly to 48 h, then 12-hourly to 180 h | 9 params — `AER_DUST`, `DUSTA`/`B`/`C`, `DUSTA0`/`B0`/`C0`, `CEIL_BSC_DUST`, `SAT_BSC_DUST` |
| 1 | Step 0 only (invariant) | `CLAT`, `CLON`, `ELAT`, `ELON`, `FR_LAND`, `HHL`, `HSURF`, `SSO_STDH` |

### 06 / 18 UTC cycles

Structurally a different product: **51 of the 97 parameters**, all meteorological, **zero ART fields**, mostly on a 6-hourly axis from 0 to +120 h (21 steps). Ensemble size is unchanged at 10 members. Re-verified on the 2026-10-18 06 and 18 UTC runs:

- **6-hourly, 0–120 h (21 steps):** `T_2M`, `T_2M_L`, `TD_2M`, `TD_2M_L`, `U_10M`/`V_10M`, `TOT_PREC`, `PMSL`, `PS`, `RELHUM_2M`, `T_G`, `Z0`, `CLCH`/`CLCL`/`CLCM`/`CLCT`/`CLCT_MOD`, `ASOB_S`, `ASOB_S_CS`, `ASOB_T`, `ATHB_S`, `ATHB_T`, `SOBS_RAD`, `THBS_RAD`, and `T`/`U`/`V`/`P`/`QV`/`QC`/`QI` on the three near-surface model levels
- **`FI` and `RELHUM` at 500 and 850 hPa only**, 21 steps. Earlier versions of this entry listed both as absent from 06/18; they are present, but on two levels only.
- **6-hourly from +6 h (20 steps):** `ASWDIR_S`, `ASWDIFD_S`, `ASWDIFU_S`, `TMAX_2M`, `TMIN_2M`
- **6-hourly to +72 h, then 12-hourly (17 steps):** `T_SO`, `H_ICE`, `H_SNOW`
- **Hourly from +1 h to +120 h:** `VMAX_10M` (120 steps)
- **Hourly to +48 h only:** `LPI_CON_MAX` and — unlike at 00/12 — `HTOP_CON` (49 steps)
- **Step 0 only:** `CLAT`, `CLON`, `ELAT`, `ELON`, `FR_LAND`, `HHL`, `HSURF`

Absent: every dust parameter (27), `USTAR`, `USTAR_THRES`, and `CAPE_ML`, `OMEGA`, `W_SO`, `TQV`/`TQC`/`TQI`, `RAIN_CON`/`RAIN_GSP`, `SNOW_CON`/`SNOW_GSP`, `FR_ICE`, `T_SNOW`, `W_SNOW`, `ALHFL_S`, `ASHFL_S`, `HBAS_CON`, `SSO_STDH`.

---

## Data availability
- **Is the data free?** Yes
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice places all its open spatial data and its EU High Value Datasets under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes — plain HTTP, no registration
- **Data format:** GRIB2, **uncompressed** (no `.bz2` wrapper on this tier). Internal `grid_ccsds` (CCSDS/AEC lossless) packing at `bitsPerValue = 16`, no bitmap. `tablesVersion = 19`, `localTablesVersion = 1`, `centre = edzw`.
- **Official download location:**
  https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eps/p/
- **Path structure:** three variants depending on the parameter's dimensionality —
  - `…/p/{PARAM}/r/{run}/e/{NN}/s/PT{hhh}H{mm}M.grib2` — single-level fields
  - `…/p/{PARAM}/lvt1/{levelType}/lv1/{level}/r/{run}/e/{NN}/s/PT{hhh}H{mm}M.grib2` — multi-level fields
  - `…/p/{PARAM}/wvl1/{nm}/lvt1/{levelType}/lv1/{level}/r/{run}/e/{NN}/s/PT{hhh}H{mm}M.grib2` — `CEIL_BSC_DUST` and `SAT_BSC_DUST` only

  where `{levelType}` is the numeric GRIB2 `typeOfFirstFixedSurface` (100 = isobaric, 106 = depth below land surface, 150 = generalized vertical height coordinate), `{run}` is ISO-8601 (`2026-10-18T12:00`, URL-encoded as `2026-10-18T12%3A00`), and `{NN}` is the zero-padded member number.

  Example: `https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eps/p/TAOD_DUST/r/2026-10-18T12%3A00/e/01/s/PT024H00M.grib2`

  **Requesting `…/{PARAM}/r/` on a multi-level parameter returns 404, not an empty listing** — the `lvt1/` segment is mandatory there. Probe `…/p/{PARAM}/` first to determine which variant applies.

- **Decoding:** DWD specifies its GRIB definitions for ecCodes v2.32.0 or newer. Verification for this entry decoded all sampled messages, including the ART parameter names, with stock ecCodes 2.48.0.
- **Run volume (server manifest, 2026-10-18/19, all 10 members):** ~198 GB / 164,590 files per 00/12 UTC run; ~21.8 GB / 15,440 files per 06/18 UTC run. A full 00/12 run is the largest single download in DWD's ICON-ART family.
- **Retention (observed):** Roughly 24 hours of rolling coverage — the four most recent runs. Older runs retain their **complete** step list; **no pruning of elapsed lead times** (re-confirmed 2026-10-19: the 2026-10-18 06 UTC run still held all 15,440 files about 18 hours after completion). The deterministic [ICON-ART](./icon-art.md) and [ICON-ART-EU](../../regional/germany/icon-art-eu.md) feeds have since been checked and behave the same way; their entries no longer describe pruning.

---

## Notes

- **Documentation-vs-reality discrepancies in DWD's own announcement.** The April 2025 Open Data announcement is the only substantive public description of these systems, and two of its statements do not match the feed:
  - It states the forecast range is +120 h with the 00/12 UTC runs of **ICON-ART (global)** as the sole exception at +180 h. In fact the 00/12 UTC runs of **ICON-ART-EPS** also reach +180 h.
  - It states temporal resolution is "as a rule hourly to +51 h, then 3-hourly." Observed transitions are at +72 h (`TAOD_DUST`) and +75 h (the 111-step meteorological group), and eight further schedules exist that the sentence does not describe at all.

  The announcement is accurate on member count (10), native-grid-only distribution, per-member file splitting, and the absence of bzip2. Its resolution (~26 km, Grid #36) was correct at the time but has been superseded by the 6 October 2026 upgrade to ~20 km, grid #39.

- **No ART output in half the cycles is the single most important operational fact.** A pipeline that polls all four cycles will find meteorology four times a day and dust twice, with no error and no warning — the dust directories simply do not exist under the 06 and 18 UTC run folders (re-confirmed 2026-10-18). Poll 00 and 12 UTC for dust.

- **The 6 October 2026 grid change splits any archive.** Grid #36 (26 km) before, grid #39 (20 km) after. Check `numberOfGridUsed` before stacking data across that date or building a climatology of member spread.

- **The ensemble is not coarser than the deterministic run.** Same grid #39, same 1,310,720 cells, same 180 h range at 00/12 UTC. This is the opposite of the ICON/ICON-EPS pairing, where the ensemble (~20 km, grid #39) runs coarser than deterministic ICON Global (~13 km, grid #26). Users pairing ICON-ART and ICON-ART-EPS are working at a single resolution, which makes the pair unusually clean for spread–error analysis.

- **`numberOfForecastsInEnsemble` is trustworthy here.** It reports 10 and ten members are published. This is worth stating explicitly because the equivalent key is unreliable in ECMWF's post-Cycle-50r1 open data (see [ENS-WAM](../../../wave_models/global/eu/ecwam-ens.md)); no such inconsistency exists on this feed.

- **`typeOfEnsembleForecast = 192` is a local code.** It sits in the WMO range reserved for local use and has no published DWD interpretation, so it conveys nothing portable. Size member arrays from the `perturbationNumber` values actually present, or from the `e/` directory listing.

- **Folder name ≠ GRIB shortName for `AER_DUST`.** The directory is `AER_DUST`; the messages inside decode as `AOD_DUST`. Keyed lookups built from directory names will miss.

- **Public data is a subset of the operational system.** 97 of the deterministic model's 139 parameter folders, most 3D fields cut to three near-surface levels, and no derived ensemble products. The full model state is not distributed.

---

## Relationship to other models

- **[ICON-ART](./icon-art.md):** The deterministic global sibling — same core, grid (#39), resolution, vertical structure, and ART configuration. Differences are in publication only: 139 parameters against 97, dust in all four cycles against two, and `DUST_MAX_TOTAL_MC_LAYER` present only in the deterministic feed.
- **[ICON-ART-EU-EPS](../../regional/germany/icon-art-eu-eps.md):** The regional ensemble counterpart, also 10 members, on the ~10 km ICON-ART-EU-NA² nest (grid #61) — the nested domain of this same system per DWD's change notice.
- **[ICON-ART-EU](../../regional/germany/icon-art-eu.md):** The regional deterministic dust forecast.
- **[ICON-ART Pollen](../../regional/germany/icon-art-pollen.md):** DWD's limited-area ICON-ART pollen forecast — a separate configuration on a separate channel (daily-mean NetCDF under `/climate_environment/health/forecasts/pollen/`). It has **no ensemble counterpart** on the `v1/m/` tier, which carries only the four dust configurations.
- **[ICON-EPS](../../../ensemble_models/global/de/icon-eps.md):** DWD's 40-member global meteorological ensemble, on the same grid #39 since 6 October 2026 (grid #36 before). The relationship to ICON-ART-EPS's 10 members is **not documented** — see *Ensemble configuration*.
- **[CAMS Global](../eu/cams-global.md):** ECMWF's IFS-based global composition ensemble-free forecast, covering a far wider species set. For probabilistic composition guidance beyond dust, CAMS rather than ICON-ART-EPS is the relevant system.

---

## Recent version history

ICON-ART-EPS has no independent change-notice series. It is upgraded as part of the wider ICON system; see the [ICON Global](../../../nwp_models/global/germany/icon-global.md#recent-version-history) and [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md#recent-version-history) entries for the full sequence.

### After 30 November 2026 — `/weather/lib/cdo/` removed
Announced in DWD's Open Data newsletter of 2 September 2026. This feed is already on the v1 URL scheme and never carried `VIS`, so the shutdown of the legacy ICON trees and the withdrawal of visibility on that date do not otherwise affect it.

### 6 October 2026 — resolution increase (effective 06 UTC run)
Per DWD's change notice of 6 October 2026 and the 28 July 2026 newsletter: ICON-ART (deterministic and the 10-member ensemble) refined from 26 km to 20 km globally (grid #36 → grid #39, 1.78× the grid points) and from 13 km to 10 km in the ICON-ART-EU-NA² nest (grid #51 → grid #61). Vertical resolution unchanged. ICON-EPS received the same global change. `CLAT`/`CLON`/`ELAT`/`ELON` changed with the grid. Test data for the 2026-07-15 00 UTC run were offered beforehand under `https://opendata.dwd.de/test/weather/nwp/v1/m/icon-art-eps/`. The CRPS gains DWD reports in the notice (typically 1–3% at the surface and in the lower troposphere) are for ICON-EPS meteorology; no dust-specific scores were given.

### 16 June 2026 — CCSDS packing
DWD switched ICON-family GRIB2 output to CCSDS packing. On this tier the files are distributed uncompressed, so the change is visible directly as the `grid_ccsds` packing type rather than through a `.bz2` wrapper.

### April 2025 — publication on DWD Open Data
DWD published the ICON-ART NWP system — global ICON-ART and ICON-ART-EPS, regional ICON-ART-EU and ICON-ART-EU-EPS — under `/weather/nwp/v1/m/`, introducing both the ART mineral-dust parameter set and DWD's revised URL scheme (ISO-8601 run and step encoding, per-member file splitting, native-grid-only distribution, GRIB-internal compression in place of bzip2).

### November 2023 — ICON-ART operational at DWD
Mineral dust forecasting with ICON-ART became technically operational, after several years of quasi-operational use under the PerduS (Prediction of Saharan Dust) project.

---

## Official documentation
- DWD Open Data Server, ICON-ART-EPS: https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eps/
- DWD Open Data announcement (ICON-ART, 8 April 2025): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_april2025_1.html
- DWD ICON-ART / COSMO-ART overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/03_environmental_forecasts/icon_art_cosmo_art_en.html
- ICON model / ART parameter documentation: https://docs.icon-model.org/atmosphere/art/art.html
- KIT ICON-ART: https://www.icon-art.kit.edu/
- DWD ICON grids at MPI-M: https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- Regridding guide: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid
- DWD Open Data newsletter, October resolution change (28 July 2026): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_juli2026_2.html
- DWD Open Data newsletter, new URL scheme / VIS / lib/cdo (2 September 2026): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD ICON-EPS / ICON-ART resolution upgrade notice (6 October 2026, `pdf_icon_eps_06_10_2026.pdf`): on DWD's ICON-EPS model-change notices page
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD GRIB definitions for ecCodes: https://opendata.dwd.de/weather/lib/grib/

### Key references
- Rieger, D., et al. (2015). ICON-ART 1.0 — a new online-coupled model system from the global to regional scale. *Geosci. Model Dev.*, 8, 1659–1676.
- Schröter, J., et al. (2018). ICON-ART 2.1: a flexible tracer framework and its application for composition studies in numerical weather prediction and climate simulations. *Geosci. Model Dev.*, 11, 4043–4068.
- Hoshyaripour, G. A., et al. (2026). The atmospheric composition component of the ICON modeling framework: ICON-ART version 2025.10. *Geosci. Model Dev.*, 19, 1645–1681.

---

*Live verification performed 2026-08-06 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eps/` (2026-08-05 06/12/18 UTC and 2026-08-06 00 UTC cycles). All 97 parameter folders enumerated for structure, level sets, cycle coverage, and step schedules; GRIB2 headers decoded with ecCodes 2.48.0; member distinctness confirmed by value comparison across members 01, 02, 05, and 10. Re-verified 2026-10-18/19 after the 6 October grid change (2026-10-18 06/12/18 UTC and 2026-10-19 00 UTC runs): parameter list, step schedules and level sets from the server manifest (`content.log.bz2`), grid and ensemble keys from decoded headers; the member-spread figures above date from August, on the old grid.*
