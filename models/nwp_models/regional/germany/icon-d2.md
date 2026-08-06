# ICON-D2 (DWD convection-permitting regional model)

## What this model is
ICON-D2 is DWD's convection-permitting (high-resolution) regional NWP configuration of ICON, run as a limited-area model over central Europe.

At 2.2 km horizontal resolution it resolves deep convection explicitly, making it well suited to short-range forecasting of small-scale, high-impact weather such as convective precipitation, thunderstorms, strong winds, and fog. ICON-D2 replaced COSMO-D2 on 10 February 2021 as part of DWD's migration from the COSMO model family to ICON.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany

---

## What area it covers
- **Coverage:** Central Europe — Germany, Switzerland, Austria, the Benelux countries, and parts of neighbouring countries
- **Native mesh extent:** 4.16°W – 20.54°E, 43.04°N – 58.17°N
- **Lat–lon product extent:** 3.94°W – 20.34°E, 43.18°N – 58.08°N (a slightly inset rectangle; see *Data availability*)
- **Topography range:** −51.93 m to 4,080.44 m (`HHL` half-level 66, excluding masked cells)

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** Yes (2.2 km; deep convection resolved explicitly)
- **Horizontal resolution:** 2.2 km — R19B07 limited-area triangular grid, **542,040 cells**
  - Verified from live GRIB2 headers: `gridType = unstructured_grid`, `numberOfDataPoints = 542040`, `numberOfGridUsed = 47`, `uuidOfHGrid = c6b12daa91ad64045b26c1b6452a2a20`. Matching grid file: `icon_grid_0047_R19B07_L.nc.bz2`. Identical grid identity to [ICON-D2-EPS](../../../ensemble_models/regional/de/icon-d2-eps.md), [ICON-D2-RUC](./icon-d2-ruc.md), and [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md).
- **Public output grids:** native triangular grid **and** a **regular** latitude–longitude grid at 0.02°, 1215 × 746 = 906,390 points (see correction below)
- **Vertical levels:** **65 full levels** (66 `HHL` half-levels), all published
- **Model top:** **22,000 m** exactly — `HHL` half-level 1, spatially constant (`bitsPerValue = 0`). *This resolves the TBD carried in earlier versions of this entry.*
- **Forecast length:** **48 hours, all eight cycles** (49 hourly steps, 0–48 h)
- **Update frequency / cycles:** 8× daily, every 3 hours (00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:** Hourly
- **Cloud microphysics:** Single-moment bulk scheme. The rapid-update sibling [ICON-D2-RUC](./icon-d2-ruc.md) uses the two-moment Seifert–Beheng scheme. The published field set shows the difference directly: ICON-D2 distributes `qc`, `qi`, `qr`, `qs`, `qg` and a `q_sedim` diagnostic, but **no `qh` (hail)** and no `tqh` — those exist only in the two-moment configurations.

> **Resolution wording — flag resolved.** Earlier versions flagged a tension between "2.2 km" and "~2.1 km average grid spacing." Both refer to the same grid (DWD grid number 47, R19B07, 542,040 cells). 2.2 km is DWD's official published figure and is used consistently across the ICON-D2 family; ~2.1 km appears in development-era COSMO General Meeting material as an average-spacing estimate. Use 2.2 km; the ~2.1 km figure is not a different configuration.

---

## Data assimilation
ICON-D2 runs a **3-hourly KENDA-LETKF** data assimilation cycle. The DA stream ingests conventional surface and upper-air observations, aircraft and radiosonde data, satellite observations, and radar information, including Latent Heat Nudging (LHN) from radar-derived precipitation.

Radar assimilation extends beyond the German C-band network: volumetric reflectivity and radial wind from 9 French radar sites were added in May 2024 via EUMETNET OPERA, extended to all 14 in-domain French sites in February 2025. LHN draws on three composites — the German RY, DWD's European EUCOM, and the OPERA European composite. Reflectivity observation errors use a vertical profile (7 dBZ near ground decreasing to 4 dBZ around 500 hPa, constant above) since July 2024, replacing a constant 10 dBZ.

The rapid-update [ICON-D2-RUC](./icon-d2-ruc.md) tightens this to an hourly cycle.

---

## Initial and boundary conditions
- **Initial conditions:** ICON-D2's own 3-hourly KENDA-LETKF analysis.
- **Boundary conditions:** lateral boundaries supplied by [ICON-EU](./icon-eu.md). Unlike the global↔ICON-EU two-way nest, ICON-D2 is a one-way-driven limited-area model.

---

## What it provides

**131 parameter directories** per cycle. Level structure verified live 2026-08-06:

| Level type | Levels | Parameters |
|---|---|---|
| Single-level | — | the bulk of the set |
| Model-level | **1–65** (1–66 for half-level fields) | `t`, `u`, `v`, `w`, `p`, `qv`, `qc`, `qi`, `qr`, `qs`, `qg`, `q_sedim`, `clc`, `tke` |
| Pressure-level | **200, 250, 300, 400, 500, 600, 700, 850, 950, 975, 1000 hPa** (11) | `fi`, `t`, `u`, `v`, `relhum`, `omega` |
| Soil-level | 0, 1, 2, 3, 5, 6, 9, 18, 27, 54, 81, 162, 243, 486, 729, 1458 | `t_so`, `w_so`, `w_so_ice`, `smi` |
| Time-invariant | — | `hhl`, `hsurf`, `clat`, `clon`, `elat`, `elon`, `fr_land`, `fr_lake`, `depth_lk`, `soiltyp`, `plcov`, `lai`, `rootdp` |

Content includes temperature, wind, precipitation by type, humidity, pressure, cloud and hydrometeor fields; convection-allowing severe-weather diagnostics (`lpi`/`lpi_max`, `uh_max`/`uh_max_low`/`uh_max_med`, `sdi_2`, `dbz_850`/`dbz_cmax`/`dbz_ctmax`, `echotop`, `tcond_max`/`tcond10_mx`, `w_ctmax`, `vorw_ctmax`, `cape_ml`/`cin_ml`); aviation-relevant fields (`ceiling`, `vis`, `hzerocl`, `snowlmt`) migrated from COSMO-D2 in February 2021; lake (FLake) variables; and **synthetic Meteosat brightness temperatures** (`synmsg_bt_cl_ir10.8`, `synmsg_bt_cl_wv6.2`).

Time-integration conventions, verified by decoding `stepType`/`stepRange` at +12 h:

| Convention | `stepType` | PDT | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | 8 | `tot_prec`, `rain_gsp`, `rain_con`, `snow_gsp`, `grau_gsp`, `runoff_s` |
| Averaged from run start | `avg` | 8 | `asob_s`, `athb_s`, `aswdir_s`, `alhfl_s` |
| Max over the preceding hour | `max` | 8 | `vmax_10m` (`11-12`), `lpi_max`, `uh_max`, `w_ctmax` |
| Max/min since last 6 h boundary | `max` / `min` | 8 | `tmax_2m` (`6-12`), `tmin_2m` |
| Instantaneous | `instant` | 0 | `t_2m`, `pmsl`, `ww`, `cape_ml`, and all multi-level fields |

`ww` (WMO weather interpretation) has 48 steps rather than 49 — no step 000. Everything else, including `vmax_10m` and `tmax_2m`, carries a step-000 file.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, bzip2-wrapped (`.grib2.bz2`)
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/icon-d2/grib/
  - Layout: `/<cycle>/<parameter>/icon-d2_germany_<grid>_<leveltype>_<YYYYMMDDHH>_<step>_<level|2d>_<param>.grib2.bz2`
  - `<grid>` is `icosahedral` or `regular-lat-lon`; single-level files carry the literal token `2d` where multi-level files carry the numeric level.
- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2
- **Retention:** each cycle directory holds exactly one run — verified 2026-08-06, all eight cycles containing a single run date each. With eight cycles, a ~24 h rolling window.
- **Volume:** **~53.4 GB and 108,589 files per run**, near-constant across cycles, so roughly **427 GB/day**. Split by grid: 22.37 GB / 54,272 files icosahedral, 30.87 GB / 54,317 files lat–lon.
- **Publication latency:** first files ~43 min 50 s after cycle time; run completes ~1 h 16 min after. Measured 2026-08-06 00 UTC: 00:43:53 → 01:59:53 UTC.

### The two grids differ in more than geometry

> **Correction to earlier versions of this entry.** This entry described the second output grid as a **rotated** lat–lon grid at 0.02°, "same spacing as the former COSMO-D2 lat-lon output". It is a **plain regular** lat–lon grid: `gridType = regular_ll`, with no rotated-pole keys present. COSMO-D2 did use a rotated grid, and the description appears to have carried over from it. The spacing is indeed 0.02°.

| | Icosahedral | Regular lat–lon |
|---|---|---|
| `gridType` | `unstructured_grid` | `regular_ll` |
| Points | 542,040 | **906,390** (1215 × 746) |
| Spacing | 2.2 km | 0.02° × 0.02° |
| Extent | native mesh | 356.06–20.34°E, 43.18–58.08°N |
| **Packing** | **`grid_ccsds`** | **`grid_simple`** |
| Masked cells (`t_2m` +12 h) | 16,968 (3.1%) | 151,528 (16.7%) |
| Parameters | 130 | 127 |

Three consequences worth planning around:

- **Only the native grid is CCSDS-packed.** The 16 June 2026 CCSDS switch applied to the icosahedral output; the lat–lon rendering is still `grid_simple`. Verified across `t_2m`, `tot_prec`, `pmsl`, and `cape_ml`. So a GRIB library without libaec can still read the lat–lon tree — the reverse of what the switch implies — while the lat–lon files are correspondingly larger (`t_2m` at +12 h: 973,926 bytes lat–lon vs 686,886 icosahedral, both bzip2-wrapped).
- **The lat–lon grid is a rectangle over a non-rectangular domain**, so 16.7% of its points are masked, against 3.1% on the native mesh (the lateral boundary relaxation zone). Both carry `bitmapPresent = 1` and `missingValue = 9999`; readers that ignore the bitmap will see 9999 as a physical value, far more of it on the lat–lon grid.
- **The parameter sets are not identical.** `clat`, `clon`, `elat`, `elon` are icosahedral-only (they are the mesh coordinates, meaningless on a regular grid). But **`mh` — mixed layer depth (`mld`, in metres) — is published only on the regular lat–lon grid**, verified live in both the manifest and the directory listing. There is no way to obtain it on the native mesh.

DWD ships CDO grid description and weight files for users interpolating the native grid themselves:
- https://opendata.dwd.de/weather/lib/cdo/
  - `icon_grid_0047_R19B07_L.nc.bz2` — grid description matching `numberOfGridUsed = 47`
  - `ICON_D2_002_EASY.tar.bz2` — weights to a 0.02° target grid over the D2 domain

---

## Notes
- ICON-D2 is the convection-permitting limited-area member of DWD's ICON suite, driven by [ICON-EU](./icon-eu.md) boundaries. Its rapid-update sibling is [ICON-D2-RUC](./icon-d2-ruc.md) — same core, same domain, same grid, same 65 levels and 22 km model top, but **hourly cycling, a +27 h range, sub-hourly output, and two-moment microphysics**.
- Ensemble counterparts: [ICON-D2-EPS](../../../ensemble_models/regional/de/icon-d2-eps.md) (3-hourly, +48 h, 20 members) and [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md) (hourly, **+27 h**, 20 members).
- **ICON-D2 is the only member of the D2 family with a lat–lon product.** ICON-D2-EPS, ICON-D2-RUC, and ICON-D2-RUC-EPS are all distributed on the native mesh only. Anyone building a workflow around the lat–lon rendering of ICON-D2 will hit a wall the moment they reach for the ensemble or rapid-update siblings.
- Standard ICON-D2 is distributed under the main `/weather/nwp/icon-d2/` path with the flat `<cycle>/<parameter>/` layout and bzip2-wrapped GRIB2. The newer `/weather/nwp/v1/m/` tier (hierarchical `p/…/r/…/s/` paths, uncompressed GRIB2) hosts ICON-D2-RUC, ICON-D2-RUC-EPS, [AICON-Global](../../global/germany/aicon-global.md), and the ICON-ART family.
- **The 48-hour range applies to all eight cycles.** Verified live — every cycle reaches step 048 with 49 hourly steps. The predecessor COSMO-D2 ran 27 h with 45 h at 03 UTC only; that asymmetry no longer exists.
- The ICON model code has been open source under a permissive licence since January 2024 (repository: https://gitlab.dkrz.de/icon/icon-model).
- **ICON-D05** — a ~500 m configuration operational since 27 February 2025 per DWD's ICON Database Reference. It is initialized from an **interpolated ICON-D2 analysis** rather than running its own data assimilation, with 3-hourly cycling and a 48 h range. Earlier versions of this entry described it as a "nested domain" spawned by ICON-D2; "driven by an interpolated ICON-D2 analysis" is the more accurate framing, and it is closer to a separate limited-area configuration than to the two-way ICON↔ICON-EU nest. **ICON-D05 does not appear anywhere under `/weather/nwp/`** as of 2026-08-06, despite being referenced in DWD's 16 June 2026 CCSDS notice as already compressed — implying an internal or restricted distribution. Not catalogued; worth re-checking for an Open Data release.

---

## Recent version history

### 18 February 2026 — model version `icon-2025.04-dwd-4.0` (effective 09 UTC run)
- **Revised ceiling diagnostic:** now uses a cloud-overlap assumption consistent with the layer-wise cloud cover fractions (`clcl`, `clcm`, …), diagnosing ceiling as the lowest layer where upward-integrated cloud fraction exceeds 50%. Fill value for grid points with too little cloud changed to **16 km above ground**, matching observation reports, rather than the height above sea level of the uppermost model level.
- **Revised visibility diagnostic:** the humidity contribution (relevant absent fog and precipitation) was reworked.
- **ICON-D2-specific:** additional output field for SSO-corrected 10 m winds; adaptive surface friction restricted to grid points with small SSO standard deviation.

### 23 July 2025 — model version `icon-2025.04-dwd-1.0` (effective 06 UTC, all ICON configurations)
Dissipative heating parameterization based on grid-scale kinetic energy loss (reduces the boundary-layer winter cold bias); ocean warm-layer parameterization introducing a diurnal SST cycle; bug fix for rime deposition on snow-free ground; retuning of interception storage and ozone–tropopause coupling.

### 4 June 2025 — DACE 2.24 (effective 09 UTC assimilation / 12 UTC forecast)
Bugfix for station identifiers in the German supplemental SYNOP network (WIGOS identifiers had displaced the older "C" identifiers needed for blacklists and redundancy checks); preparations for NOAA-21 satellite data; quality-control fixes for radiosonde and aircraft humidity.

### 7 May 2025 — saturation vapour pressure coefficients, `icon-2024.10-dwd-4.0` (effective 09 UTC ICON-D2 run)
Magnus-formula Tetens coefficients replaced with the more accurate ECMWF set, reducing errors by at least a factor of four at temperatures far below freezing. Applied consistently across model, data assimilation, and verification. DWD flagged this as preparation for an upcoming cloud microphysics upgrade.

### 26 February 2025 — KENDA: full French radar network (effective 06 UTC assimilation / 09 UTC forecast)
DACE 2.23 removed the computational cap on French radar assimilation; **all 14 French radar sites within the ICON-D2/RUC domain** are now assimilated, up from 9. Measurable Fraction Skill Score improvement for precipitation in the south-western quarter of the domain.

### 5 February 2025 — `uuidOfVGrid` change (all ICON configurations)
The GRIB2 `uuidOfVGrid` parameter changed due to new compiler options. Present only for model full- and half-level variables; pressure-level and single-level fields unaffected. Actual model level heights did **not** change for ICON-D2. Consumers keying on `uuidOfVGrid` for vertical-grid identity needed to update at this date.

### 4 December 2024 — model version `icon-2024.10-dwd-2.0` (effective 09 UTC run)
Extended adaptive parameter tuning (soil hydraulic diffusivity, land albedo, snow cover fraction diagnosis); revised treatment of snow cover in surface transfer calculation, reducing surface fluxes over snow beneath high vegetation.

### 9 July 2024 — gust parameterization and radar DA, `icon-2024.01-dwd-3.1` (effective 09 UTC run)
- Gust parameterization revised to use 10-minute averages of 10 m wind speed instead of instantaneous values, with excess gust speed limited relative to the resolved maximum wind in the lowest 1500 m (tuning factor 1.75 for ICON-D2). RMS error reduced ~5%, with significant improvement in the 20 m s⁻¹ and 25 m s⁻¹ gust categories.
- Latent Heat Nudging input migrated to HDF5 OPERA and EUCOM composites.
- Radar reflectivity observation errors changed from a constant 10 dBZ to a vertical profile — a change made for the then-upcoming ICON-RUC, with near-neutral impact on ICON-D2 itself.

### 22 May 2024 — KENDA: French radar volumes introduced (effective 06 UTC assimilation / 09 UTC forecast)
First assimilation of volumetric reflectivity and radial wind from **9 French radar stations** via EUMETNET OPERA. Also: removal of a rudimentary bias correction for near-saturated humidity observations, and correction of the vertical influence of SYNOP observations in the LETKF.

### 23 April 2024 — near-surface visibility output (effective 09 UTC run)
`VIS` (near-surface visibility) added as an output variable for ICON-D2 and ICON-EU, requested by DWD's aviation forecasting offices for use alongside ceiling in VFR forecasting.

### 24 January 2024 — sea-ice bottom heat flux, `icon-2.6.6-nwp2` (effective 12 UTC run)
Sea-ice scheme revised to account for ocean-to-ice heat flux; external parameter bugfix for false glacier points; adaptive time-step reduction extended to horizontal CFL exceedances.

### 29 November 2023 — all-sky satellite assimilation (effective 06 UTC assimilation / 09 UTC forecast)
Change to the ICON-D2 KENDA system introducing all-sky satellite radiance handling.

### 20 June 2023 — SST bugfix (effective 09 UTC run)
End of unintentional use of the ICON-EU sea-surface-temperature field in ICON-D2. Database requests had been supplying ICON-EU SST valid at 03 UTC, which then persisted until the ICON-D2 SST analysis at 00 UTC the following day. No forecast-quality impact expected, but SST time series spanning this date will show a discontinuity.

### 10 February 2021 — COSMO-D2 → ICON-D2
ICON-D2 replaced COSMO-D2 as part of DWD's migration from the COSMO model family to ICON. All aviation weather products migrated at the same time.

---

## Official documentation
- DWD ICON-D2 model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_d2/icon_d2_node.html
- DWD NWP forecast data overview: https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD ICON-D2 change notices: https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_d2_gesamt.html
- DWD ICON change notices (all-configuration changes): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD CDO grid description and weight files: https://opendata.dwd.de/weather/lib/cdo/
- ICON open source repository: https://gitlab.dkrz.de/icon/icon-model

### Key references
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Schraff, C., Reich, H., Rhodin, A., Schomburg, A., Stephan, K., Periáñez, A., and Potthast, R. (2016). *Kilometre-scale ensemble data assimilation for the COSMO model (KENDA).* Quarterly Journal of the Royal Meteorological Society, 142(696), 1453–1472. https://doi.org/10.1002/qj.2748
- Reinert, D., et al. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*

---

*Live verification performed 2026-08-06 against `https://opendata.dwd.de/weather/nwp/icon-d2/grib/` (all eight cycles, 2026-08-05 03 UTC through 2026-08-06 00 UTC) and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.48.0.*
