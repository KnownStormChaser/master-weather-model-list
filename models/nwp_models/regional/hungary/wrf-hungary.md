# WRF Hungary

## What this model is
WRF Hungary is a **convection-permitting regional deterministic numerical weather prediction (NWP) model** run by HungaroMet (Hungarian Meteorological Service / OMSZ) over the Carpathian Basin at 1.5 km resolution. HungaroMet runs it four times a day within the framework of its **nowcasting project**, producing short-range (0–36 h) hourly forecast fields oriented toward high-impact and convective weather — the public parameter set includes derived maximum radar reflectivity, wind gusts, cloud layers, and grid-scale precipitation.

Despite being operated under the "nowcasting project" umbrella, WRF is a full NWP model rather than a nowcasting system in the technical sense (it is not an observation-extrapolation or sub-hourly blending product). HungaroMet's dedicated analysis/nowcasting system is **MEANDER**, listed separately on the HungaroMet model menu. WRF is therefore catalogued here as a regional NWP sibling to [AROME Hungary](./arome-hungary.md), with which it shares the HungaroMet open data portal and the same convection-permitting short-range role. *(Placement note for review — see Notes.)*

---

## Who runs it
- **Organization:** HungaroMet (Hungarian Meteorological Service, formerly OMSZ — Országos Meteorológiai Szolgálat)
- **Country / region:** Hungary

---

## What area it covers
- **Coverage:** Hungary and the wider Carpathian Basin (Central Europe)
- **Domain details (confirmed from the distributed NetCDF global attributes):**
  - Grid: **499 (west–east) × 394 (north–south)** points (`Nx`/`Ny`)
  - Northwest (first) grid point: **lat 49.93735°N, lon 13.93832°E** (`La1`/`Lo1`), **north→south** row order
  - Grid spacing: **dx = 0.021236°** (E–W), **dy = 0.01428°** (N–S) (`Dx`/`Dy`)
  - **Computed extent:** ≈ **13.94°E – 24.51°E**, **44.33°N – 49.94°N** (derived from NW corner + grid spacing × dimensions)
- **Projection:** Regular latitude–longitude (spherical). Files declare `conventions = "LFS"` with no CF coordinate variables or `grid_mapping`; build lat/lon axes from `La1`/`Lo1` + `Nx`/`Ny`/`Dx`/`Dy`, noting the north→south row order — same pattern as [MEANDER](../../../nowcasting_models/regional/hungary/meander.md) and the [HungaroMet radar composites](../../../../observations/radar/hungary/hungaromet-radar.md)

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** WRF (Weather Research and Forecasting). The dataset description does not specify the dynamical core; WRF-ARW is the near-universal operational/research core, but this is **not explicitly documented** (TBD). The distributed files include AFWA-diagnostics fields (`PSEALVLC`, `CLOUD_BASE` carry `AFWA Diagnostic:` long names) — an ARW post-package, which is *suggestive* of an ARW core but not definitive
- **Code version:** TBD (not stated; the dataset description notes only that "the WRF system is updated once a year")
- **Convection-allowing:** Yes (1.5 km grid; deep convection explicitly resolved)
- **Horizontal resolution:** **1.5 km** (regular lat–lon grid; dx 0.021236° / dy 0.01428°)
- **Vertical levels:** TBD (not stated in the dataset description)
- **Model top:** TBD
- **Forecast length:** **0 – 36 hours**
- **Update frequency:** **4× daily (00/06/12/18 UTC)**
- **Temporal output resolution:** 1 hour

---

## Data assimilation
- **Data assimilation:** Not documented. The public dataset description does not state whether the HungaroMet WRF runs its own analysis (e.g. WRFDA) or operates purely as downscaling from a driving model (TBD).

---

## Initial and boundary conditions
- **Initial conditions:** TBD (not documented)
- **Lateral boundary conditions:** TBD — the driving global/host model is not stated in the dataset description (TBD)

---

## What it provides
Deterministic short-range forecast fields, distributed as one variable per file.

**2-D surface fields:**

| Variable | Description | Unit |
|---|---|---|
| `T2` | 2 m temperature | K |
| `U10` | 10 m wind, west–east component | m/s |
| `V10` | 10 m wind, south–north component | m/s |
| `maxLogz` | Derived maximum (column) radar reflectivity | dBZ |
| `PSEALVLC` | Mean sea-level pressure | Pa |
| `CLOUD_BASE` | Cloud base height | m |
| `CLOUD_HIGH` | High cloud cover | octa |
| `CLOUD_MID` | Middle cloud cover | octa |
| `CLOUD_LOW` | Low cloud cover | octa |
| `CLOUD_TOTAL` | Total cloud cover | octa |
| `RAIN_TOT` | Accumulated total grid-scale precipitation | mm |
| `WGUST` | Wind gust | m/s |
| `SWDOWN` | Downward short-wave flux at ground surface | W/m² |
| `PBLH` | Planetary boundary layer height | m |
| `SNOWH` | Surface snow height | m |

**3-D fields on pressure levels (1000, 850, 700, 500 hPa):**

| Variable | Description | Unit |
|---|---|---|
| `T` | Temperature | K |
| `u` | Eastward wind | m/s |
| `v` | Northward wind | m/s |
| `RelHum` | Relative humidity | % |
| `Geopot` | Geopotential height | m |

**Boundary-layer level (`_h100`):**

| Variable | Description | Unit |
|---|---|---|
| `T_pbl` | ⚠ File `long_name` is **"perturbation potential temperature theta"** with `levelType = "PBLLevel"` — *not* plain air temperature. Whether the distributed values are temperature, full potential temperature, or WRF perturbation θ is unclear from the file (observed range ~280–302 K). Flagged pending confirmation | K |

---

## Data availability
- **Is the data free?** Yes
- **License:** HungaroMet Open Data Portal terms (identical to HungaroMet's other ODP datasets, e.g. [AROME Hungary](./arome-hungary.md): free use without modification; attribution required as *"Database: Meteorological Database, HungaroMet Nonprofit Zrt."*; modifications require written consent, with example attribution forms in the General Terms of Use. Warnings, alerts, and aviation forecasts may only be redistributed unmodified.)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (one variable per file, compressed inside ZIP archives — `.nc.zip`)
- **Official download location:**
  https://odp.met.hu/weather/nwp/WRF/nc/
- **File naming conventions** (from the dataset description):
  - 2-D fields: `WRF-<variable>-<YYYYMMDD>_<HHmm>+<TTTtt>.nc.zip`
  - 3-D pressure-level fields: `WRF-<variable>_p<pressure_level>-<YYYYMMDD>_<HHmm>+<TTTtt>.nc.zip`
  - 3-D height-level fields: `WRF-<variable>_h<height_level>-<YYYYMMDD>_<HHmm>+<TTTtt>.nc.zip`
  - where `<HHmm>` is the run initial time (UTC) and `<TTTtt>` is the lead time in hours (TTT) and minutes (tt)

---

## Notes
- **Taxonomy / placement (for review):** The dataset description says WRF "runs four times a day within the framework of the nowcasting project of the Hungarian Meteorological Service." Despite the "nowcasting" wording, the system is a deterministic NWP model (0–36 h range, 4×/day cycling, hourly output) rather than a nowcasting system under the repository's definition (observation extrapolation / sub-hourly blend, ~0–6 h). HungaroMet's actual nowcasting/analysis product is **MEANDER**, listed separately. Catalogued here under regional NWP accordingly — flagging in case you'd prefer a cross-reference from the nowcasting category.
- **Sibling models at HungaroMet:** WRF is one of HungaroMet's publicly distributed forecast models alongside [AROME Hungary](./arome-hungary.md) (2.5 km AROME) and the [CHIMERE Hungary](../../../air_quality_models/regional/hungary/chimere-hungary.md) air-quality forecast. The HungaroMet map-model menu also lists ECMWF, AROME, WRF, and MEANDER. Of HungaroMet's in-house NWP, both AROME and WRF are openly distributed on ODP (ALADIN/HU and AROME-EPS are not).
- **Convective orientation:** The public field set is geared toward short-range/convective applications — derived maximum radar reflectivity (`maxLogz`), wind gusts, layered cloud cover, and grid-scale precipitation — consistent with the nowcasting-project context.
- **Largely undocumented configuration:** The dynamical core, code version, vertical levels, model top, data assimilation, and driving/host model are **not documented**. Direct inspection of the distributed `.nc` files (at +25 h) confirmed these cannot be recovered from the data either: the files are post-processed into HungaroMet's stripped **"LFS"** format and carry only `history`, `title`, `version`, `conventions`, and the grid corner/spacing — all WRF namelist/global metadata is removed. These fields remain TBD pending a more detailed HungaroMet source.
- **`title = "MM5 OUTPUT"` / `version = 9991.0` (legacy artifacts):** Every WRF file's `title` reads `MM5 OUTPUT` and `version` is `9991.0` — the same placeholder seen in MEANDER. A post-processor artifact; it does **not** indicate an MM5 core or a real version number.
- **Trust values over units strings:** Several `long_name`/`units` attributes are truncated or garbled (MSLP units render as `Parcent`, reflectivity as `dbZcent`, `CLOUD_BASE` units as `mm` despite values of 125–10939 m). Confirm variable identity by physical range, not the units string. `PSEALVLC` values (~101500–102200) confirm it is genuinely **Pa** — no MEANDER-style hPa trap.

---

## Revision history
- The dataset description states the **WRF system is updated once a year**. No version labels or dated upgrade history are published in the source on file.

---

## Official documentation
- HungaroMet WRF page (Hungarian): https://www.met.hu/idojaras/elorejelzes/modellek/WRF/
- HungaroMet WRF page (English): https://www.met.hu/en/idojaras/elorejelzes/modellek/WRF/
- WRF public dataset description (PDF): https://odp.met.hu/weather/nwp/WRF/Description_shortrange_forecast-WRF-en.pdf 
- HungaroMet open data portal (root): https://odp.met.hu/
- HungaroMet ODP WRF download folder: https://odp.met.hu/weather/nwp/WRF/nc/
- WRF model home (NCAR/UCAR): https://www.mmm.ucar.edu/weather-research-and-forecasting-model

### Contact
- Open data technical contact: odp@met.hu
- Licensing / consent for modifications: service@met.hu
