# GEPS (Global Ensemble Prediction System)

## What this model is
The Global Ensemble Prediction System (GEPS) is Canada's global ensemble numerical weather prediction system, providing probabilistic medium-range forecasts and quantifying forecast uncertainty from the medium range into the extended range.

GEPS is the ensemble counterpart to the deterministic [GDPS](../../../nwp_models/global/canada/gem-global.md), built on the same Global Environmental Multiscale (GEM) atmospheric model and run as a coupled atmosphere–ocean–sea ice ensemble using NEMO and CICE for the ice-ocean component. It generates 20 perturbed members plus 1 control by combining perturbed initial conditions from a 256-member ensemble data assimilation system with stochastic representations of model uncertainty.

GEPS plays a central operational role within ECCC's prediction suite: it provides the perturbed background fields used to construct the flow-dependent background-error covariances for the deterministic [GDPS](../../../nwp_models/global/canada/gem-global.md), [RDPS](../../../nwp_models/regional/canada/rdps.md), and [HRDPS](../../../nwp_models/regional/canada/hrdps.md) 4DEnVar analyses, supplies initial and lateral boundary conditions for the regional [REPS](../../regional/canada/reps.md) ensemble, and contributes 21 members (1 control + 20 perturbed) to the multi-center [NAEFS](../usa/naefs.md) and [557th WW GEPS](../usa/557wg-geps.md) products.

The current operational version is **GEPS 8.1.0**, implemented **14 April 2026** as an infrastructure port to ECCC's new supercomputing platform. The last scientific upgrade was **GEPS 8.0.0** (11 June 2024, 12 UTC run), part of Innovation Cycle 4 (IC-4) alongside GDPS 9.0.0 and RDPS 9.0.0, which raised horizontal resolution from ~39 km to ~25 km, moved the assimilation cycle to a Local Ensemble Transform Kalman Filter (LETKF) recentered on a 4DEnVar analysis, upgraded the sea ice component to CICE 6.2.0 with Delta-Eddington radiation, and extended the reforecast system from 32 to 39 days with twice-weekly cycles.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Canadian Centre for Meteorological and Environmental Prediction (CCMEP), Environment and Climate Change Canada (ECCC)
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global
- **Computational grid:** Yin–Yang overlapping grids at uniform 0.23° (~25 km). The GEPS 8.0.0 fact sheet gives the native dimensions as 1249 × 834.
- **Distributed grid (`raw/`):** Single global regular latitude–longitude grid, **720 × 361**, **0.5°**, first grid point 90°S 0°E, spanning 90°S→90°N and 0°→359.5°E. Live-verified on the 2026-08-08 12 UTC run (`centre: cwao`, `gridType: regular_ll`, `shapeOfTheEarth: 6`, radius 6371229 m).
- **Distributed grid (`products/`):** **A different grid — see the caution below.** 721 × 360, 0.5°, spanning 90°S→89.5°N and 180°W→180°E with the ±180° meridian duplicated.
- **Ice-ocean grid:** Global 0.25° with 50 z-levels (shared with GDPS via GIOPS initialization)

> ⚠️ **The two distribution trees are not on the same grid, and the documentation only describes one of them.**
>
> | | `raw/` (members) | `products/` (derived) |
> |---|---|---|
> | Ni × Nj | 720 × 361 | **721 × 360** |
> | Grid points | 259,920 | 259,560 |
> | Longitude span | 0° → 359.5°E | **−180° → +180°** (721 columns; the ±180° meridian appears twice) |
> | Latitude span | −90° → **+90°** | −90° → **+89.5°** (no 90°N row) |
> | `scanningMode` | 64 | 64 |
>
> Both are 0.5° `regular_ll` scanning north from the south pole, so a careless read of the headers makes them look interchangeable. They are not: a raw member and a products percentile for the same parameter and step cannot be overlaid without a longitude roll and a regrid. The MSC Datamart page publishes a single grid table (`ni 720`, `nj 361`, first point 90°S 000°E) which describes the `raw/` tree only; the `products/` geometry is undocumented.
>
> Verified across all 533 GRIB2 messages decoded from 41 distinct `products/` parameter files on the 2026-08-08 12 UTC run — the geometry is uniform across every product, parameter, and step, so this is structural rather than an isolated encoding fault.

> **The open-data grid is coarser than the model.** At 0.5° (~55 km at the equator) the distributed grid undersamples a ~25 km forecast. This is the opposite of the situation in [GDWPS](../../../wave_models/global/canada/gdwps-canada.md) and [GEWPS](../../../wave_models/global/canada/gewps-canada.md), where the distribution grid is finer than the computational grid. The GEPS 8.0.0 fact sheet also documents a **1440 × 721 (0.25°) global lat-lon "user grid"**; no 0.25° GEPS product appears anywhere on the Datamart, and this entry has not been able to verify where — or whether — that grid is distributed (**TBD**).

---

## Basic details
- **Model type:** Global ensemble NWP, coupled to ocean and sea ice
- **Model system / core:** GEM (Global Environmental Multiscale) version 5.2.3, two-way coupled to NEMO 3.6 ocean and CICE 6.2.0 sea ice
- **Dynamical formulation:** Hydrostatic primitive equations
- **Convection-allowing:** No (~25 km horizontal resolution)
- **Ensemble size:** 21 (1 control + 20 perturbed)
- **Horizontal resolution:** ~25 km (0.23° quasi-uniform Yin–Yang grid); 0.5° on distribution
- **Vertical levels:** 84 staggered hybrid levels (Charney–Phillips vertical grid)
- **Model top:** 0.1 hPa
- **Forecast length:**
  - 16 days (384 h) on the 00 and 12 UTC medium-range cycles
  - Extended to 39 days (936 h) on Monday and Thursday **00 UTC** runs, supporting subseasonal anomaly products
  - 72 h on the additional 06 and 18 UTC early forecast cycles, used to provide initial and boundary conditions for [REPS](../../regional/canada/reps.md)
- **Update frequency / cycles:** 2× daily medium-range runs (00, 12 UTC) plus 4× daily early forecast runs (00, 06, 12, 18 UTC) that pilot REPS. **Only the 00 and 12 UTC medium-range cycles are published on the Datamart** — the 06 and 18 UTC early runs are internal.
- **Time step:** 900 seconds (forecast); 720 seconds (12 min) for the LETKF trial-field integrations
- **Time integration:** Implicit, semi-Lagrangian (3D), 2 time-level (Côté et al., 1998a, 1998b)
- **Numerical technique:** Finite differences on the Arakawa C grid (horizontal) and Charney–Phillips grid (vertical)
- **Temporal output resolution:** Tree-dependent — see *What it provides*. Briefly: `raw/` is 3-hourly to 192 h then 6-hourly to 384 h (then 6-hourly to 936 h on extended days); `products/` is 3-hourly to 384 h for a subset of parameters, with several coarser tiers.
- **Processing time:** ~1 h 30 min for the 16-day forecast on the pre-2026 back-end (`underhill`/`robert`), per the 8.0.0 technical specifications. Not restated for the April 2026 infrastructure (**TBD**).

---

## Data assimilation
- **Data assimilation:** Yes — GEPS runs its own ensemble analysis cycle, distinct from the GDPS deterministic 4DEnVar analysis but tightly integrated with it
- **Method:** **Local Ensemble Transform Kalman Filter (LETKF)** (Buehner, 2020) with cross-validation, run with **256 members** subdivided into 8 sub-ensembles of 32 members each. Sub-ensemble membership is randomly redrawn at each analysis time. The LETKF analyses are recentered on a companion 4DEnVar analysis with a height-dependent recentering coefficient ("hybrid gain")
- **4DEnVar recentering analysis:** Run on the same 25 km Yin–Yang grid as the LETKF, using the LETKF ensemble-mean background trajectory as first guess
- **Background trial fields:** 3- to 9-hour forecasts from a 256-member coupled GEM ensemble integrated with stochastic perturbations (SPP and SKEB, see below) and homogeneous isotropic model-error random fields applied additively to the analysis (Houtekamer et al. 2009, 2014). Observations themselves are no longer perturbed
- **Cadence:** 4× daily (00, 06, 12, 18 UTC) with a 6-hour assimilation window centred on the synoptic hour
- **Cut-off times:**
  - 7 hours for final analyses (00, 06, 12, 18 UTC)
  - 3 hours for the 00 and 12 UTC medium-range forecast runs (subsampled 20-member subset, mean constrained to match the full 256-member ensemble mean)
  - 2 hours for the 00, 06, 12, 18 UTC early forecast runs that pilot REPS
- **Initialization:** Incremental Analysis Update (IAU) (Bloom et al. 1996; Buehner et al. 2015)
- **Radiative transfer model:** RTTOV v13
- **Analysis variables:** T, Ps, U, V, q on 84 hybrid levels
- **Notable observations assimilated (LETKF):** Radiosonde upper-air and surface, surface stations (SYNOP BUFR, SWOB, METAR), aircraft, atmospheric motion vectors, scatterometer winds (including HSCAT from HY-2B and HY-2C since October 2024), ATOVS level 1b (AMSU-A and AMSU-B/MHS, both GLOBAL and RARS sources), GPS-RO, ATMS, AIRS, IASI, CrIS. ATMS and CrIS from NOAA-21 added May 2025.
- **Additional observations used in the recentering 4DEnVar:** Ground-based GPS-RO, clear-sky radiances (CSR), and SSMIS radiances, in addition to the LETKF observation set
- **Background check and bias correction:** Performed within the GEPS analysis cycle itself

The 256-member LETKF ensemble at the heart of GEPS does double duty: it produces the GEPS perturbed initial conditions, and the 256 perturbed background trajectories are used directly as the flow-dependent background-error covariances in the deterministic GDPS, RDPS, and HRDPS hybrid 4DEnVar analyses. This shared analysis ensemble is the core architectural link between Canada's deterministic and ensemble systems.

---

## Perturbations and design
- **Initial condition perturbations:** Each of the 20 perturbed members is initialized from a different LETKF analysis selected from the 256-member set, with the subsample chosen so that its mean matches the full 256-member ensemble mean. The control member (member 0) is initialized from the recentered ensemble-mean analysis. All members share the same ocean and sea ice initial conditions, taken from the [GIOPS](../../../ocean_models/global/canada/giops.md) analysis (Smith et al. 2016)
- **Model uncertainty representation:** Two complementary stochastic schemes (McTaggart-Cowan et al. 2022a, b):
  - **Stochastic Parameter Perturbation (SPP):** **22 physics parameters and algorithmic choices** (Appendix A of the GEPS 8.0.0 technical specifications) are perturbed continuously in space and time using Markovian random fields. Perturbed elements span deep convection (`kfctrig4`, `kfctrigwh`, `kfctrigwl`, `deeprate`, `dpdd_mult`, `crad_mult`, `mid_minemf`), boundary-layer mixing (`fh_mult`, `fm_mult`, `fnnreduc`, `ml_emod`, `ricmin`, `tkediff`), cloud microphysics and radiation (`cond_hcst`, `rew_mult`, `rei_mult`, `aero_mult`, `hu0max`, `hu0min`), gravity-wave drag (`rmscon`, `sgo_phic`), and semi-Lagrangian advection accuracy (`adv_rhsint`). Most elements use a 36-hour autocorrelation decay with γ = 1.4; the deep-convection triggers use γ = 2.8, and `adv_rhsint` uses a 24-hour decay. In v8.0.0, the SPP element distributions are recentered around the new control member, the range for `adv_rhsint` is reduced to address tropical over-dispersion, and the stretching parameter γ is increased by a factor of 2·ln 2 across all elements to compensate for the increased variance from the improved Markovian field representation
  - **Stochastic Kinetic Energy Backscatter (SKEB):** Adds wind increments proportional to diagnosed dissipation from explicit horizontal diffusion, parameterized gravity-wave drag, and (since GEPS 7.0) deep-convective momentum transfer (Charron et al. 2010; Shutts 2005). In v8.0.0 the backscattering fraction `ens_skeb_alph` was reduced from 1.0 to a more physical value of 0.7
- **Control member:** Receives no model uncertainty perturbation. Its physics configuration is fine-tuned starting from the 25 km GDPS configuration to align with the deterministic system at the new resolution

The control member is built from the recentered ensemble mean rather than from an independent deterministic analysis — a design choice that keeps the ensemble statistically consistent with its own analysis cycle while remaining tightly tied to GDPS through the shared LETKF backgrounds and 4DEnVar recentering.

---

## Coupled ice-ocean component
- **Ocean model:** NEMO 3.6 (Madec 2008)
- **Sea ice model:** CICE 6.2.0 (Hunke and Lipscomb 2010; Hunke et al. 2021), upgraded from earlier CICE versions in v8.0.0
- **Sea ice physics (new in v8.0.0):**
  - Radiation scheme: Delta-Eddington (R_ice = R_pnd = R_snw = 2.0; Briegleb and Light 2007)
  - Conductivity scheme: "bubbly" (Pringle et al. 2007)
- **Coupling:** Two-way atmosphere–ocean–ice coupling, with new GEM–NEMO coupling weights introduced in v8.0.0
- **Sea surface temperature and ice cover:** Initialized from the CMC GIOPS analysis, then evolved by the GEPS oceanic and sea-ice components
- **Sea ice thickness:** Derived from climatology, stamped by the GIOPS analysis at initial time

---

## Member packaging and GRIB2 encoding

**All 21 members are concatenated into a single GRIB2 file per parameter per step.** There is no member token in the filename — the `allmbrs` suffix is a constant string, not a member identifier.

Live-verified on the 2026-08-08 12 UTC run (ecCodes 2.48.0):

| Key | Value |
|---|---|
| `edition` | 2 |
| `centre` | `cwao` (54), `subCentre` 0 |
| `tablesVersion` | **4** |
| `generatingProcessIdentifier` | 70 |
| `typeOfGeneratingProcess` | 4 (ensemble forecast) |
| `productDefinitionTemplateNumber` | **1** for instantaneous fields, **11** for interval fields |
| `numberOfForecastsInEnsemble` | 21 |
| `perturbationNumber` | 0–20 (0 = control) |
| `typeOfEnsembleForecast` | **1** (control) / **4** (perturbed) |
| `packingType` | `grid_jpeg` (JPEG 2000) |
| `bitsPerValue` | 10, 12, or 16 depending on parameter |

> ⚠️ **`typeOfEnsembleForecast = 4` means "Multi-model forecast" in WMO Code Table 4.6.** GEPS is a single-model ensemble, so the value does not describe the product. The control is stamped `1` ("Unperturbed low-resolution control forecast"), which is also inaccurate — the control runs at the same 25 km resolution as every perturbed member. **Discriminate members on `perturbationNumber`, never on `typeOfEnsembleForecast`.** The same key is set to 255 (missing) in ECMWF's [ENS-WAM](../../../wave_models/global/eu/ecwam-ens.md) and to a local code 192 in the [FNMOC Ensemble](../usa/fnmoc-ensemble.md); GEPS is a third variation on the same hazard.

> ⚠️ **Interval fields use PDT 4.11, not 4.1.** `APCP`, `TMAX`, `TMIN`, `OLR` and the surface flux accumulations are encoded as individual ensemble forecasts over a time interval. Readers that filter on `productDefinitionTemplateNumber == 1` will silently drop every accumulated and extreme-value field.

### Parameters that stock ecCodes cannot name

Eight of the 97 `raw/` parameters return `shortName = unknown` and `units = unknown` under ecCodes 2.48.0:

| File token | discipline/category/number | Note |
|---|---|---|
| `DSWRF_SFC_0` | 0/4/192 | local parameter |
| `USWRF_SFC_0` | 0/4/193 | local parameter |
| `DLWRF_SFC_0` | 0/5/192 | local parameter |
| `ULWRF_SFC_0` | 0/5/193 | local parameter |
| `OLR_NTAT_0` | 0/5/193 | local parameter — **same triplet as `ULWRF_SFC_0`** |
| `SWAT_DBLL_10cm` | 2/0/192 | local parameter |
| `APCP_SFC_0` | 0/1/8 | **standard WMO number**, still unmatched |
| `TCDC_SFC_0` | 0/6/1 | **standard WMO number**, still unmatched |

Two things to note. First, `OLR_NTAT_0` and `ULWRF_SFC_0` carry an **identical parameter triplet** and are separable only by level type (`nominalTop` vs `surface`) — since neither decodes to a name, a pipeline keying on `shortName` will collapse them. Second, `APCP` and `TCDC` use ordinary WMO parameter numbers and still fail to resolve; re-encoding `tablesVersion` from 4 to 32 does **not** fix either, so the old master-table stamp is not the cause. Whatever the mechanism, the practical consequence is the same: **map GEPS parameters from the filename token, not from the decoded GRIB name.**

`PWAT_EATM_0` and `TCDC_SFC_0` additionally report `typeOfLevel = unknown` (`typeOfFirstFixedSurface = 200`, entire atmosphere layer), which will unsettle cfgrib and xarray loaders.

### Precision varies by level

`bitsPerValue` is 12 for most instantaneous fields and 16 for accumulations, but the **40 m, 80 m and 120 m fields are stored at 10 bits** — `TMP_TGL_40/80/120`, `SPFH_TGL_40/80/120`, `WIND_TGL_40/80/120`. Measured quantization on the 2026-08-08 12 UTC run, step 012:

| Field | Bits | Smallest resolved step |
|---|---|---|
| `WIND_TGL_10` | 12 | 0.01 m s⁻¹ |
| `WIND_TGL_80` | **10** | **0.05 m s⁻¹** |
| `TMP_TGL_2m` | 12 | 0.05 K |
| `TMP_TGL_80` | **10** | **0.125 K** |

These are precisely the hub-height levels wind-energy users want, and they are the least precisely packed fields in the dataset.

---

## What it provides

Two parallel trees under the same root: `raw/` (all 21 members) and `products/` (derived statistics and probabilities). They differ in grid (see above), step list, parameter naming, and parameter set — they are best treated as two datasets that happen to share a directory.

### `raw/` — ensemble members

**97 parameters** at each step from 0 to 384 h. Live-verified 2026-08-08 12 UTC.

**Isobaric fields (56 files/step), on an inconsistent level set:**

| Parameter | Levels (hPa) | Count |
|---|---|---|
| `HGT` | 10, 50, 100, 200, 250, 300, 500, 700, 850, 925, 1000 | 11 |
| `TMP` | 10, 50, 100, 200, 250, 500, 700, 850, 925, 1000 | 10 |
| `RH` | 10, 50, 100, 200, 250, 500, 700, 850, 925, 1000 | 10 |
| `UGRD` | 10, 50, 100, 200, 250, 300, 400, 500, 700, 850, 925, 1000 | 12 |
| `VGRD` | 10, 50, 100, 200, 250, 300, 400, 500, 700, 850, 925, 1000 | 12 |
| `VVEL` | 850 | 1 |

Union is 12 levels, but **no parameter carries all 12**: winds have 300 and 400 hPa, geopotential height has 300 but not 400, and temperature and humidity have neither. Vertical velocity exists at 850 hPa only. (The MSC readme's "some fifteen vertical levels" does not correspond to any count in the distributed data.)

**Near-surface and single-level fields (41 files/step):**

- **Screen level:** `TMP_TGL_2m`, `RH_TGL_2m`, `SPFH_TGL_2`, `TMAX_TGL_2m`, `TMIN_TGL_2m`
- **Wind-energy levels:** `TMP`, `SPFH`, `WIND` at 40, 80 and 120 m
- **10 m wind:** `UGRD_TGL_10m`, `VGRD_TGL_10m`, `WIND_TGL_10`
- **Precipitation (accumulated from run start):** `APCP` (total), `ARAIN` (rain), `ASNOW` (snow), `AFRAIN` (freezing rain), `AICEP` (ice pellets)
- **Surface / soil:** `PRES_SFC_0`, `PRMSL_MSL_0`, `SNOD`, `WEASD`, `SFCWRO` (surface runoff), `TSOIL_DBLL_10cm`, `SWAT_DBLL_10cm`, `ICETK` (sea ice thickness, discipline 10)
- **Fluxes and radiation (accumulated):** `DSWRF`, `USWRF`, `DLWRF`, `ULWRF`, `SHTFL`, `LHTFL`, `OLR_NTAT`
- **Columnar / convective:** `PWAT_EATM`, `TCDC`, `CAPE`, `CIN`

**Parameter availability is step-dependent:**

| Step | Files | Note |
|---|---|---|
| 000 | 84 | No accumulations, fluxes, or interval extremes |
| 003, 009, 015, … 189 | 95 | Accumulations and fluxes present; no `TMAX`/`TMIN` |
| 006, 012, … 192, 198, … 384 | 97 | Full set (`TMAX`/`TMIN` appear only on 6-hourly steps) |

**Step list:** 3-hourly 000–192, then 6-hourly 198–384. **97 steps, 9,332 files, ~30.9 GiB per cycle.**

### `raw/` — the 39-day extension

On **Monday and Thursday 00 UTC only**, the `raw/` tree continues from 390 h to **936 h in 6-hourly steps** (92 additional steps). Verified on the 2026-08-03 and 2026-08-06 00 UTC runs; the 2026-08-07 00 UTC run stops at 384 h, as does every 12 UTC run.

The extended segment carries a **reduced 75-parameter set**. Dropped beyond 384 h: `CAPE`, `CIN`, `SFCWRO`, `TMAX_TGL_2m`, `TMIN_TGL_2m`, `USWRF`, `ULWRF`, the entire 40/80/120 m block (`TMP`, `SPFH`, `WIND` — 9 files), `WIND_TGL_10`, and `UGRD`/`VGRD` at 300 and 400 hPa. Members and encoding are unchanged (21 members, `perturbationNumber` 0–20, 720 × 361).

**Extended segment: 6,900 files, ~23.1 GiB.**

### `products/` — derived statistics and probabilities

**41 distinct parameters**, but never all at once — availability is a function of step and of the statistical window. **No step 000.** Live-verified across all 128 steps of the 2026-08-08 12 UTC run.

**Instantaneous parameters, by cadence tier:**

| Cadence | Parameters |
|---|---|
| 3-hourly, 003–384 | `TEMP_TGL_2m`, `TCDC_SFC_0`, `HEATX_TGL_2m`, `WCF_TGL_2m`, `WIND_TGL_10m` |
| 3-hourly to 240, then 6-hourly | `WIND_ISBL_0250`, `WIND_ISBL_0850` |
| 3-hourly to 072, then 6-hourly | `HGT_ISBL_0500`, `PRMSL_MSL_0`, `TEMP_ISBL_0850`, `MUCAPE_SFC_0`, `VWSH_ISBL_850-250`, `VWSH_ISBL_925-700` |

**Interval parameters, by window:**

| Window | Parameters | Step cadence |
|---|---|---|
| 3 h max | `WIND-Max-3h`, `GUST-Max-3h` | 3-hourly, **003–072 only** |
| 6 h max | `WIND-Max-6h`, `GUST-Max-6h` | 6-hourly, 006–384 |
| 6 h accum | `TPRATE-Accum-6h`, `SPRATE-Accum-6h`, `IPRATE-Accum-6h` | 3-hourly to 072, then 6-hourly |
| 12 h max | `WIND-Max-12h`, `GUST-Max-12h` | 12-hourly |
| 12 h accum | `TPRATE`, `RPRATE`, `SPRATE`, `IPRATE`, `FPRATE` `-Accum-12h` | 12-hourly |
| 24 h accum | `RPRATE`, `SPRATE`, `IPRATE`, `FPRATE` `-Accum-24h` | **12-hourly** |
| 24 h accum | `TPRATE-Accum-24h` | 3-hourly to 072, then 6-hourly |
| 24 h extreme | `TEMP-Max-24h`, `TEMP-Min-24h`, `HEATX-Max-24h`, `WCF-Min-24h` | 24-hourly |
| 48/72/96/120 h accum | `TPRATE-Accum-48h`, `-72h`, `-96h`, `-120h` | 24-hourly |

Note the three different cadences among the nominally "24-hour" windows. `TPRATE-Accum-24h` is a rolling window published up to eight times a day; the other four precipitation-type 24 h accumulations are published twice a day; the 24 h temperature extremes once a day.

**Step counts range from 7 files (at steps like 189 and 195, where only the five 3-hourly instantaneous parameters plus two wind levels survive) to 38 files (at 24-hour multiples beyond 072). 128 steps, 2,240 files, ~2.5 GiB per cycle.**

**Products never extend past 384 h** — including on Monday and Thursday, when the raw members run to 936 h. The 39-day extension is members-only; all subseasonal probability guidance must be computed by the user.

### Product types inside each `products/` file

Each file bundles every published statistic for its parameter, using four product definition templates:

| PDT | Content | Discriminating key |
|---|---|---|
| 2 | Derived forecast, instantaneous | `derivedForecast` |
| 6 | Percentile, instantaneous | `percentileValue` |
| 9 | Probability, over a time interval | `probabilityType` + limit |
| 12 | Derived forecast, over a time interval | `derivedForecast` |

`derivedForecast` values in use are **0** (unweighted mean), **4** (spread of all members), **8** (minimum), and **9** (maximum). `percentileValue` is drawn from {10, 25, 50, 75, 90}. `probabilityType` is **3** (above lower limit) for precipitation, wind and warm-temperature thresholds, and **4** (below upper limit) for `TEMP-Min-24h` and `WCF-Min-24h`. All probability fields are in percent, 0–100 (verified across 256 probability messages).

The suite is **not uniform across parameters** — file contents range from 2 messages to 24:

| Parameter | Msgs | Percentiles | Derived | Probability thresholds |
|---|---|---|---|---|
| `HGT_ISBL_0500`, `PRMSL_MSL_0` | 2 | — | mean, spread | — |
| `MUCAPE`, `VWSH_*`, `TEMP_ISBL_0850` | 5 | 25/50/75 | min, max | — |
| `TCDC_SFC_0` | 7 | 10/25/50/75/90 | min, max | — |
| `WIND_ISBL_0250/0850` | 7 | 25/50/75 | all four | — |
| `TEMP_TGL_2m`, `WCF_TGL_2m`, `HEATX_TGL_2m`, `WIND_TGL_10m` | 9 | 10/25/50/75/90 | all four | — |
| `GUST-Max-12h` | 3 | — | — | >10, >15, >25 m s⁻¹ |
| `GUST-Max-3h/6h` | 8 | 25/50/75 | min, max | >15, >25, >35 m s⁻¹ |
| `WIND-Max-3h/6h` | 4 | — | — | 4 thresholds — **see caution** |
| `WIND-Max-12h` | 23 | 10/25/50/75/90 | all four | 14 thresholds |
| `TPRATE-Accum-24h/48h/72h/96h/120h` | 23 | 10/25/50/75/90 | all four | 14 (1 → 200 mm) |
| `TEMP-Max-24h` | 24 | 10/25/50/75/90 | all four | 15 (>243.14 → >313.14 K) |
| `TEMP-Min-24h` | 23 | 10/25/50/75/90 | all four | 14 (<233.14 → <298.14 K) |
| `WCF-Min-24h` | 20 | 10/25/50/75/90 | all four | 11 (<223.14 → <273.14 K) |

> ⚠️ **`WIND-Max-3h` and `WIND-Max-6h` express their thresholds in km/h inside a field declaring m s⁻¹.**
>
> Both files carry thresholds of **36, 54, 72 and 90** with `scaleFactorOfLowerLimit = 0`, in a parameter (0/2/1) whose declared units are `m s**-1`. Read literally, the first would be the probability of a 6-hour maximum 10 m wind above 36 m s⁻¹ (130 km/h) — yet its global mean value is 21.8%.
>
> The 6-hour window (378–384 h) is nested inside the 12-hour window (372–384 h), so P(6 h max > X) ≤ P(12 h max > X) must hold at every grid point for the same X. Testing against the 12-hour file's genuine 10.0 m s⁻¹ threshold gives correlation **0.9815** with **zero violations** of the nesting constraint. Testing against 32.778 m s⁻¹ (the nearest 12-hour threshold under the literal reading) gives correlation 0.038, with 70% of points violating nesting — physically impossible.
>
> **Treat 36 / 54 / 72 / 90 as 10 / 15 / 20 / 25 m s⁻¹.** Whether ECCC regards this as a defect or an undocumented convention is not stated in any located source (**TBD**); what is verified is the numerical meaning.
>
> `WIND-Max-12h` is unaffected — it encodes genuine m s⁻¹, and mixes two threshold families in one file: round values (10, 15, 25 m s⁻¹) alongside km/h-derived conversions (5.5556 = 20 km/h, 8.3333 = 30, 10.278 = 37, 11.111 = 40, 13.889 = 50, 17.222 = 62, 18.056 = 65, 20.833 = 75, 24.444 = 88, 27.778 = 100, 32.778 = 118).

> ⚠️ **`numberOfForecastsInEnsemble = 20` on every derived-product message**, against the 21 members that demonstrably exist in the `raw/` tree. The MSC documentation describes products as "created from all members." The value plausibly counts perturbed members only, but nothing states whether the control is included in the statistics (**TBD**). Do not use this key to size member arrays.

> ⚠️ **`units` is unreliable on probability messages.** Within a single `TPRATE-Accum-24h` file, ecCodes reports `%` for some probability messages and `kg m**-2` for others, with no difference in product type. All probability messages hold percentages regardless of what the key says.

> **Precipitation parameters are encoded as rates but are accumulations.** `TPRATE`, `RPRATE`, `SPRATE`, `IPRATE` and `FPRATE` use the WMO *precipitation rate* parameter numbers (0/1/52, 65, 66, 67, 68) with `typeOfStatisticalProcessing = 1` (accumulation) over the stated window. Values and thresholds are in kg m⁻² (mm), not kg m⁻² s⁻¹ — the MSC documentation's stated unit of kg/(m²·s) is incorrect. Note also that the `raw/` tree uses a **different parameter for total precipitation**: `APCP` is 0/1/8, while `products/` `TPRATE` is 0/1/52.

### Naming diverges between the two trees

Parameter and level tokens are not shared:

| Quantity | `raw/` | `products/` |
|---|---|---|
| Temperature | `TMP` | `TEMP` |
| 10 m wind speed | `WIND_TGL_10` | `WIND_TGL_10m` |
| Total precipitation | `APCP_SFC_0` (0/1/8) | `TPRATE-Accum-*h_SFC_0` (0/1/52) |
| Rain / snow / freezing rain / ice pellets | `ARAIN`, `ASNOW`, `AFRAIN`, `AICEP` | `RPRATE`, `SPRATE`, `FPRATE`, `IPRATE` |
| Convective instability | `CAPE` (surface-based, 0/7/6) | `MUCAPE` (most-unstable, **local 0/7/238**) |

`MUCAPE` is a local ECCC parameter and decodes as `unknown`; it is also **not the same quantity** as the raw tree's `CAPE`, so the products tree offers no probabilistic version of the raw CAPE field and vice versa.

> ⚠️ **Level tokens in `raw/` filenames are internally inconsistent.** The 2 m level appears as `2m` in `TMP_TGL_2m`, `RH_TGL_2m`, `TMAX_TGL_2m`, `TMIN_TGL_2m` but as bare `2` in `SPFH_TGL_2`. The 10 m level appears as `10m` in `UGRD_TGL_10m` and `VGRD_TGL_10m` but as bare `10` in `WIND_TGL_10`. The 40/80/120 m levels never carry a unit suffix. Any URL template built by string-substituting a level into a pattern will 404 on some parameters.

---

## Reforecast system
A reforecast procedure similar to Hagedorn (2008) has run since 2015 (Gagnon et al. 2013b, 2014b).

- **Coverage:** 20 years
- **Cadence:** Twice weekly (Monday and Thursday 00 UTC), extended in v8.0.0 from the previous Thursday-only schedule
- **Forecast length:** 39 days (extended from 32 days in v7.0)
- **Members per reforecast date:** 4 (reduced from the 21 used operationally to control computational cost)
- **Effective historical database:** ~80 reforecasts per calendar date (4 members × 20 years)
- **Atmospheric initial conditions:** ERA5 reanalyses with random homogeneous isotropic perturbations applied to the 4 members (same amplitude as in earlier GEPS versions; Houtekamer et al. 2009)
- **Land surface initial conditions:** Generated by running the offline Surface Prediction System (SPS; Carrera et al. 2010) forced with ERA5 at the lowest model level (rather than the diagnostic level), to maintain consistency with the operational forecast system
- **Ocean initial conditions:** ORAS5 reanalysis (Zuo et al. 2015)
- **Sea ice initial conditions:** Had2CIS (HadISST2.2 combined with digitized CIS sea ice charts over the Arctic and Great Lakes) prior to January 2016; CMC GIOPS analysis from January 2016 onward — chosen to minimize discontinuity given Had2CIS and GIOPS share a similar climatology
- **Sea ice thickness:** ORAS5 monthly reanalysis
- **Stochastic perturbations:** Same SPP and SKEB schemes as the operational forecast system, including the v8.0.0 improvements

The fifth forecast week added in v8.0.0 and the new Monday cycle together expand both the lead-time coverage and the sample size of the reforecast database used for skill calibration of the monthly forecast.

**The reforecast database is not distributed on the MSC Datamart.** Only real-time forecasts are published; no reforecast archive path was located (**TBD**).

---

## Relationship to other models
- **[GDPS](../../../nwp_models/global/canada/gem-global.md):** Deterministic counterpart, sharing the GEM atmospheric core, the NEMO/CICE ice-ocean components, and the LETKF ensemble at the heart of ECCC's data assimilation system. GDPS uses GEPS's 256-member LETKF backgrounds as its full ensemble-derived B matrix; GEPS in turn uses a 4DEnVar recentering analysis run on the same grid as GDPS
- **[RDPS](../../../nwp_models/regional/canada/rdps.md) and [HRDPS](../../../nwp_models/regional/canada/hrdps.md):** Both deterministic regional systems also draw flow-dependent background-error covariances from the same 256-member GEPS LETKF ensemble
- **[REPS](../../regional/canada/reps.md):** Regional short-range ensemble nest, initialized and laterally driven by the GEPS 06/18 UTC early forecast cycles
- **[GEWPS](../../../wave_models/global/canada/gewps-canada.md):** Global ensemble wave system, each member forced by the matching GEPS member's 10 m winds and ice concentration
- **[RESPS](../../../storm_surge_models/regional/canada/resps.md):** Regional ensemble storm surge system, members mapping one-to-one onto the 21 GEPS members
- **[NAEFS](../usa/naefs.md):** North American multi-center bias-corrected ensemble combining 21 GEPS members with 31 GEFS members for a 52-member product. Note that the NAEFS feed republishes the Canadian members on NCEP infrastructure under an entirely different naming convention and with a 45-field subset
- **[557th WW GEPS](../usa/557wg-geps.md):** U.S. Air Force statistical multi-model ensemble (note the acronym collision — see Notes) that uses the Canadian GEPS as one of three contributing single-center ensembles

---

## Data availability
- **Is the data free?** Yes (no registration required for MSC Open Data)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence (attribution required; commercial use permitted) — https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (JPEG 2000 packing; no `.idx` or byte-range sidecars are published)
- **Official download location:**
  https://dd.weather.gc.ca/today/ensemble/geps/grib2/
  - **Path template:** `https://dd.weather.gc.ca/today/ensemble/geps/grib2/{TYPE}/{HH}/{hhh}/` — `{TYPE}` = `raw` or `products`, `{HH}` = `00` or `12`, `{hhh}` = 3-digit forecast hour
  - **Filename conventions:**
    - `CMC_geps-raw_{Var}_{LevelType}_{Level}_latlon0p5x0p5_{YYYYMMDDHH}_P{hhh}_allmbrs.grib2`
    - `CMC_geps-prob_{Var}_{LevelType}_{Level}_latlon0p5x0p5_{YYYYMMDDHH}_P{hhh}_all-products.grib2`
  - **Dated archive:** `https://dd.weather.gc.ca/{YYYYMMDD}/WXO-DD/ensemble/geps/grib2/…` — note the `WXO-DD/` path component, which is absent from the `/today/` form
- **Retention:** ~30 days rolling (dated directories from 2026-07-10 present on 2026-08-08). There is no long-term open archive of GEPS on the Datamart.
- **Push notification:** MSC Datamart AMQP (Sarracenia) feed announces files as they land
- **Also available via:** MSC GeoMet WMS/WCS/OGC API (rendered and coverage services — out of scope for this catalog as raw gridded data, but useful for reference)
- **Per-cycle volume:**

  | Tree | Steps | Files | Size |
  |---|---|---|---|
  | `raw/` 000–384 | 97 | 9,332 | ~30.9 GiB |
  | `products/` 003–384 | 128 | 2,240 | ~2.5 GiB |
  | `raw/` 390–936 (Mon/Thu 00 UTC) | 92 | 6,900 | ~23.1 GiB |

  Roughly **67 GiB/day** for the two standard cycles, rising to ~90 GiB on Mondays and Thursdays.

---

## Notes
- GEPS is part of ECCC's Innovation Cycle 4 (IC-4) prediction suite. The June 11, 2024 implementation upgraded GEPS, GDPS, and RDPS together, with the 25 km GEPS LETKF ensemble providing the shared analysis backbone for all three deterministic-ensemble pairs.
- The April 14, 2026 upgrade to v8.1.0 was an infrastructure port to ECCC's new supercomputing platform, with no documented scientific change. The same migration produced GEWPS 1.4.0 and RESPS 1.8.0 on the same date. **The technical specifications and fact sheet PDFs have not been reissued for 8.1.0** — the documents linked as "current" both describe 8.0.0 and are dated 11 June 2024.
- Open data licensing is genuinely open — no registration required, direct file access via the Datamart. Same as GDPS, RDPS, HRDPS, GIOPS, and RIOPS.
- **Published documentation for this dataset is incomplete and partly wrong.** Worth knowing before building against it:
  - The datamart page's *List of variables → Individual members* heading is **empty**. There is no published list of the 97 `raw/` parameters; the only routes are server enumeration or the companion [XML element list](https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwp_geps/geps_element.xml).
  - The documented forecast-hour list (`000, 003, … 192, 198, … 384`) describes `raw/` only. The `products/` tree is 3-hourly throughout with no step 000, and contains steps (195, 201, 207 …) that the documentation says do not exist.
  - The single documented grid table describes `raw/` only (see the grid caution above).
  - The products list is inaccurate in both directions: `HGT` and `PRMSL` are documented with minimum and maximum but ship only mean and spread; `MUCAPE`, `TCDC`, `VWSH` and others ship min/max that are not documented; `TPRATE` and `RPRATE` carry an undocumented 200 mm threshold and `SPRATE` an undocumented 100 mm one.
  - Minimum and maximum are documented as "0 percentile" and "100th percentile" but are encoded as `derivedForecast` 8 and 9, **not** as `percentileValue` 0 and 100. Code selecting on `percentileValue` will never find them.
  - The readme describes the 0.5° grid as "about 39km" — a leftover from the pre-8.0.0 39 km era, doubly wrong now that the model is 25 km and the grid is ~55 km at the equator.
  - The changelog nests the 26 May 2025 entry inside the 14 April 2026 section and omits it from the page's table of contents.
- As with all ensemble systems, GEPS output should be interpreted probabilistically rather than as a single forecast. The system's value is in calibrated probabilities and ensemble spread, not in any single-member view.
- The acronym "GEPS" is used for two unrelated systems in operational forecasting. The Canadian GEPS described here (Global Ensemble Prediction **System**) is Environment Canada's single-center coupled global ensemble. A separate product called [557th WW GEPS](../usa/557wg-geps.md) (Global Ensemble Prediction **Suite**) is a U.S. Air Force multi-model statistical ensemble that uses the Canadian GEPS as one of its inputs.

---

## Recent version history

### GEPS v8.1.0 — operational April 14, 2026 (current)
Port to ECCC's new high-performance computing infrastructure, completed 14 April 2026 and applied across the operational suite. No scientific or configuration change is documented; the technical specifications remain those of v8.0.0. The same migration produced [GEWPS](../../../wave_models/global/canada/gewps-canada.md) 1.4.0 and [RESPS](../../../storm_surge_models/regional/canada/resps.md) 1.8.0.

### Data assimilation updates within v8.0.0
- **26 May 2025** (06 UTC run) — ATMS and CrIS observations from NOAA-21 added
- **2 April 2025** (06 UTC run) — revised SSMIS thinning scheme admitting more data; correction to the azimuth angle used in slant-path computation for AMSU-A and MHS radiances
- **23 October 2024** (06 UTC run) — sea surface wind observations from the HSCAT scatterometer on HY-2B and HY-2C added

### GEPS v8.0.0 — operational June 11, 2024
Implemented at the 12 UTC run on June 11, 2024 as part of Innovation Cycle 4 (IC-4), alongside GDPS 9.0.0 and RDPS 9.0.0.

Headline changes from v7.0:
- **Horizontal resolution increased from ~39 km to ~25 km** (Yin–Yang grid at 0.23° uniform resolution)
- **GEM upgraded to version 5.2.3** for the forecast component (5.2.1 for the assimilation component)
- **LETKF analysis recentered on a 4DEnVar analysis** with a height-dependent (rather than uniform) recentering coefficient — the "hybrid gain" approach revisited and aligned with the GDPS configuration
- **RTTOV upgraded to v13**
- **All-sky observation handling improvements:** correction to the water saturation functions; modification of hyperspectral infrared QC for albedo changes; updated source for sea-ice snow depth and ice thickness
- **Sea ice component upgraded to CICE 6.2.0** with **Delta-Eddington radiation** (R_ice = R_pnd = R_snw = 2.0) and **"bubbly" thermal conductivity** scheme — same configuration as GIOPS 3.5.0 and GDPS 9.0.0
- **New GEM–NEMO coupling weights**
- **SPP and SKEB schemes improved** through better-represented Markovian perturbation fields. SPP element distributions are recentered around the new control; the range for `adv_rhsint` is reduced to address tropical over-dispersion; the stretching parameter γ is increased by 2·ln 2 across all SPP elements; the SKEB backscattering fraction `ens_skeb_alph` is reduced from 1.0 to a more physical 0.7
- **Background check and bias correction now performed within GEPS** itself
- **Geophysical fields regenerated** for the 25 km grid using the unified `prep_geophy` package, with scale-selective filtered topography (McTaggart-Cowan et al. 2019)
- **Reforecast system extended:**
  - Forecast length extended from 32 to 39 days (adding a fifth forecast week)
  - Cadence doubled from weekly (Thursday 00 UTC) to twice-weekly (Monday and Thursday 00 UTC)
  - Reforecast members reduced to 4 to control computational cost while maintaining ~80 reforecasts per calendar date across the 20-year period
  - Sea ice initial conditions from CMC GIOPS analysis from January 2016 onward (replacing Had2CIS), with no significant climatological discontinuity

### Earlier versions
- **v7.1.1 → v8.0.0:** several DA-only updates 2022–2023 (ship BUFR surface observations, GOES-18 AMVs, Sentinel-6A GPS-RO, Spire and PlanetIQ commercial GNSS-RO, snow depth QC correction)
- **v7.1.0 — 28 June 2022:** HPC infrastructure port
- **v7.0.0 — 1 December 2021:** major upgrade
- **v6.1.0 — 21 January 2020:** HPC infrastructure port
- **v6.0.0 — 3 July 2019:** GEM 4.8-LTS.16; first coupling to NEMO and CICE; ocean and sea-ice initial conditions from GIOPS 3.0.0; ECMWF-style hybrid gain recentering introduced
- **v5.0.0 — 18 September 2018:** 50 km Gaussian grid replaced by 39 km Yin–Yang; model top raised from 2 hPa to 0.1 hPa; IAU replaced digital filter initialization; products moved to a 25 km user grid
- **v4.1.1 — 15 December 2015:** reforecast period extended from 18 to 20 years
- **v4.0.0 — 18 November 2014:** EnKF ensemble size raised from 192 to 256; coupling with 4D-EnVar for background-error covariances
- **v3.1.0 — 4 December 2013:** 32-day monthly forecasts introduced (Thursday 00 UTC); reforecast database introduced
- **v3.0.0 — 13 February 2013:** GEM 4.4.1; ISBA-only surface scheme

---

## Official documentation
- GEPS overview: https://eccc-msc.github.io/open-data/msc-data/nwp_geps/readme_geps_en/
- GEPS on the MSC Datamart (file naming, grid, product list): https://eccc-msc.github.io/open-data/msc-data/nwp_geps/readme_geps-datamart_en/
- Technical specifications (linked as current; content is v8.0.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_specifications/tech_specifications_GEPS_e.pdf
- Technical note: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_geps_e.pdf
- Fact sheet (v8.0.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_geps_e.pdf
- GEPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_geps/changelog_geps_en/
- CMC operational suite changelog (v8.1.0 HPC migration): https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- System dependency diagram: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_GEPS_en.svg
- Variable element list (XML, EN/FR): https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwp_geps/geps_element.xml
- Discovery metadata: https://open.canada.ca/data/en/dataset/6d9dd2f8-202e-58cb-a110-e2168832aacb
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Buehner, M. (2020). Local Ensemble Transform Kalman Filter with Cross Validation. *Mon. Wea. Rev.*, 148, 2265–2282.
- Buehner, M., et al. (2015). Implementation of Deterministic Weather Forecasting Systems Based on Ensemble–Variational Data Assimilation at Environment Canada. Part I: The Global System. *Mon. Wea. Rev.*, 143, 2532–2559. https://doi.org/10.1175/MWR-D-14-00354.1
- Charron, M., G. Pellerin, L. Spacek, P. L. Houtekamer, N. Gagnon, H. L. Mitchell, and L. Michelin (2010). Toward Random Sampling of Model Error in the Canadian Ensemble Prediction System. *Mon. Wea. Rev.*, 138, 1877–1901.
- Côté, J., et al. (1998a). The Operational CMC-MRB Global Environmental Multiscale (GEM) Model: Part I — Design Considerations and Formulation. *Mon. Wea. Rev.*, 126, 1373–1395.
- Houtekamer, P. L., X. Deng, H. L. Mitchell, S.-J. Baek, and N. Gagnon (2014). Higher Resolution in an Operational Ensemble Kalman Filter. *Mon. Wea. Rev.*, 142, 1143–1162.
- Houtekamer, P. L., H. L. Mitchell, and X. Deng (2009). Model Error Representation in an Operational Ensemble Kalman Filter. *Mon. Wea. Rev.*, 137, 2126–2143.
- McTaggart-Cowan, R., L. Separovic, R. Aider, M. Charron, M. Desgagné, P. L. Houtekamer, D. Paquin-Ricard, P. Vaillancourt, and A. Zadra (2022a). Using stochastic parameter perturbations to represent model uncertainty, Part I: Implementation and parameter sensitivity.
- McTaggart-Cowan, R., L. Separovic, M. Charron, D. Xingxiu, N. Gagnon, P. L. Houtekamer, and A. Patoine (2022b). Using stochastic parameter perturbations to represent model uncertainty, Part II: Comparison with existing techniques in an operational ensemble.
- McTaggart-Cowan, R., et al. (2019). Modernization of Atmospheric Physics Parameterization in Canadian NWP. *J. Adv. Model. Earth Syst.*, 11. https://doi.org/10.1029/2019MS001781
- Shutts, G. (2005). A kinetic energy backscatter algorithm for use in ensemble prediction systems. *Q.J.R. Meteorol. Soc.*, 131, 3079–3102.
- Smith, G. C., et al. (2016). Sea ice forecast verification in the Canadian Global Ice Ocean Prediction System. *Q.J.R. Meteorol. Soc.*, 142, 659–671. https://doi.org/10.1002/qj.2555
- Zuo, H., M. A. Balmaseda, and K. Mogensen (2015). The new eddy-permitting ORAP5 ocean reanalysis. *Climate Dynamics*. https://doi.org/10.1007/s00382-015-2675-1
