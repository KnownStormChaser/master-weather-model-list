# NWPS (Nearshore Wave Prediction System) – NOAA/NWS

## What this model is
The **Nearshore Wave Prediction System** is NOAA's operational high-resolution coastal wave model for U.S. National Weather Service forecast offices. Unlike a scheduled global or regional wave model, **each of 36 coastal Weather Forecast Offices (WFOs) runs its own domain on demand**: a run is triggered when the office submits its forecaster-edited wind grids from AWIPS, and NCEP then runs the SWAN spectral wave model over that office's waters to 144 hours.

NWPS forecasts the nearshore wave field — wave height, period and direction, swell, and individually tracked wave systems — with water level and currents included. At most offices it also produces **coastal hazard guidance**: hourly rip current probability and wave runup, setup, swash, total water level, and dune erosion and overwash probabilities. The current release is **NWPS v1.5** (package `nwps.v1.5.10`), operational since 9 November 2026.

---

## Who runs it
- **Organization:** NOAA / National Weather Service. Developed by NCEP's Environmental Modeling Center with the coastal WFOs; run in NCEP's production suite by NCEP Central Operations. NOAA/NOS, USGS and USACE are named partners for the coastal hazard guidance.
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Coastal waters of 36 NWS forecast offices — the U.S. East, Gulf and West Coasts, Alaska, Hawaii, Guam, and Puerto Rico/U.S. Virgin Islands. The Great Lakes are **not** covered (see [GLWU](./glwu.md)).
- **Domain details:** One domain per office, grouped on NOMADS into five NWS regions. **The `pr` region is Pacific Region (Honolulu and Guam), not Puerto Rico**, which is San Juan (`sju`) in the Southern Region (`sr`). Live-verified on NOMADS, 15–19 November 2026:

| Region (dir) | WFO | Office | CG0 | Nested grids | Cycles seen | Runs/day | Hazard guidance |
|---|---|---|---|---|---|---|---|
| Alaska (`ar`) | `aer` | Anchorage | Yes | CG2, CG3 | 06/18Z | 2.0 | — |
| Alaska (`ar`) | `afg` | Fairbanks | — | CG2, CG3 | 06/18Z | 2.0 | — |
| Alaska (`ar`) | `ajk` | Juneau | Yes | CG2, CG3 | 00/06/12/18Z | 3.2 | — |
| Alaska (`ar`) | `alu` | Anchorage (Aleutians) | Yes | CG2, CG3 | 06/18Z | 2.0 | — |
| Eastern (`er`) | `akq` | Wakefield VA | Yes | — | 00/06/12Z | 2.6 | rip + runup |
| Eastern (`er`) | `box` | Boston | Yes | CG2 | 00/12Z | 2.0 | rip + runup |
| Eastern (`er`) | `car` | Caribou ME | Yes | CG2, CG3 | 00/06/12Z | 2.8 | rip + runup |
| Eastern (`er`) | `chs` | Charleston SC | Yes | CG2, CG3 | 00/06/12/18Z | 3.4 | rip + runup |
| Eastern (`er`) | `gyx` | Gray/Portland ME | Yes | CG2, CG3 | 00/06/12/18Z | 4.0 | rip + runup |
| Eastern (`er`) | `ilm` | Wilmington NC | Yes | — | 00/06/12/18Z | 3.4 | rip + runup |
| Eastern (`er`) | `lwx` | Baltimore/Washington | Yes | — | every 3 h | 6.6 | — |
| Eastern (`er`) | `mhx` | Newport/Morehead City NC | Yes | CG2–CG5 | 00/12Z | 1.8 | rip + runup |
| Eastern (`er`) | `okx` | New York | Yes | CG2–CG4 | 00/12Z | 1.6 | rip + runup |
| Eastern (`er`) | `phi` | Mount Holly NJ | Yes | CG2, CG3 | 00/06/12/18Z | 3.4 | rip + runup |
| Pacific (`pr`) | `gum` | Guam | Yes | CG2–CG4 | 00/06/12/18Z | 4.0 | rip only |
| Pacific (`pr`) | `hfo` | Honolulu | Yes | CG2–CG5 | 00/06/12/18Z | 2.2 | rip only |
| Southern (`sr`) | `bro` | Brownsville | Yes | — | 00/12Z | 2.0 | rip + runup |
| Southern (`sr`) | `crp` | Corpus Christi | Yes | — | 00/06/12Z | 2.4 | rip + runup |
| Southern (`sr`) | `hgx` | Houston/Galveston | Yes | CG2 | 00/12/18Z | 2.5 | rip + runup |
| Southern (`sr`) | `jax` | Jacksonville | Yes | CG2–CG5 | 00/06/12/18Z | 3.2 | rip + runup |
| Southern (`sr`) | `key` | Key West | Yes | CG2 | 00/06/12/18Z | 3.8 | rip only |
| Southern (`sr`) | `lch` | Lake Charles | Yes | — | 00/12Z | 2.0 | rip + runup |
| Southern (`sr`) | `lix` | New Orleans | Yes | CG2 | 00/12Z | 1.8 | rip + runup |
| Southern (`sr`) | `mfl` | Miami | Yes | CG2–CG5 | 00/12/18Z | 2.2 | rip + runup |
| Southern (`sr`) | `mlb` | Melbourne FL | Yes | CG2 | 00/12Z | 1.8 | rip + runup |
| Southern (`sr`) | `mob` | Mobile | Yes | CG2–CG5 | 00/12Z | 2.0 | rip + runup |
| Southern (`sr`) | `sju` | San Juan | Yes | CG2, CG3 | 00/06/12/18Z | 4.0 | rip only |
| Southern (`sr`) | `tae` | Tallahassee | Yes | CG2–CG5 | 00/12Z | 2.0 | rip + runup |
| Southern (`sr`) | `tbw` | Tampa Bay | Yes | CG2 | 00/12Z | 2.0 | rip + runup |
| Western (`wr`) | `eka` | Eureka | Yes | CG2, CG3 | 00/12Z | 1.8 | rip + runup |
| Western (`wr`) | `lox` | Los Angeles | Yes | CG2, CG3 | 00/06/12/18Z | 4.0 | rip + runup |
| Western (`wr`) | `mfr` | Medford | Yes | CG2–CG5 | 00/12Z | 2.0 | rip + runup |
| Western (`wr`) | `mtr` | San Francisco Bay Area | Yes | CG2–CG4 | 00/12Z | 1.8 | rip + runup |
| Western (`wr`) | `pqr` | Portland OR | Yes | CG2–CG5 | 00/02/12/14Z | 3.4 | rip + runup |
| Western (`wr`) | `sew` | Seattle | Yes | CG2, CG3 | 00/12Z | 2.0 | rip + runup |
| Western (`wr`) | `sgx` | San Diego | Yes | CG2, CG3 | 00/12Z | 2.0 | rip + runup |

  "Runs/day" averages the days each office appeared in the five-day window, so it describes recent practice, not a fixed schedule. Cycles, run counts and which nested grids appear vary with what each office requests.

---

## Basic details
- **Model type:** Deterministic wave model, run on demand per office
- **Grid system:** Per-office computational domains. **Most domains now compute on unstructured meshes**, and the native-mesh results are interpolated onto regular latitude–longitude output grids for distribution:
  - 10 offices moved to unstructured meshes at v1.2 (2018): TBW, MFL, SJU, MHX, AKQ, OKX, BOX, CAR, SGX, HFO.
  - 12 at v1.3 (2021): HGX, MOB, TAE, KEY, MLB, JAX, CHS, ILM, PHI, GYX, ALU, GUM.
  - 12 at v1.5 (2026): EKA, LOX, LWX, BRO, LCH, LIX, PQR, MTR, AER, AFG, AJK, MFR.
  - By those notices only Seattle (moved to a higher-resolution structured grid at v1.5) and Corpus Christi (not named in any of them, TBD) still compute on regular grids.
- **Output grids:**
  - **CG1** — the whole domain; integral wave parameters plus, at most offices, hazard fields.
  - **CG2–CG5** — up to four nested grids over harbours, inlets and bars.
  - **CG0** — a coarser copy of the CG1 domain carrying the wave-system tracking (swell partition) output. Not produced for Fairbanks (`afg`).
  - At v1.5, Eureka and Seattle gained a CG3 that carries a separate wind-only run over the CG1 extent.
- **Core wave model:** SWAN — **version 41.10** in the live spectral output headers. Wave boundary conditions come from WAVEWATCH III.
- **Horizontal resolution:** Domain-dependent. Live-verified output grid spacing on 2026-11-18:
  - **CG1:** from about 1.4 km (Seattle, 0.013° × 0.009°) to about 7.5 km (Aleutians, 0.113° × 0.068°). Miami is about 2 km (0.0197° × 0.018°) and Honolulu about 2.6 km.
  - **Nested grids:** about 200 m (e.g. Miami CG2, 0.002°), down to **about 50 m on Eureka's new Humboldt Bay grid** (CG2, 0.0006° × 0.00046°).
  - **Native meshes:** 5 km to 200 m on the unstructured meshes, per the notices.
- **Forecast length:** **144 h** (live-verified: `f000`–`f144`, 145 hourly steps, in all ten sampled files across seven offices and all grid types)
- **Update frequency / cycles:** **On demand.** An office's run starts when it submits wind grids, usually at 00/06/12/18Z, typically 1–4 times a day and up to 8. Off-synoptic cycles occur (Portland OR at 02Z and 14Z; Baltimore/Washington every three hours). There is no fixed schedule, so a given office may not run at a given cycle.
- **Temporal output resolution:** Hourly (gridded and point output)

---

## Forcing and nesting
- **Wind forcing:** **Forecaster-edited wind grids** from each WFO's AWIPS Graphical Forecast Editor. These are human-adjusted forecasts, not raw NWP output: NWPS is driven by the official NWS wind forecast itself. If an office's winds are incomplete or erroneous, the system falls back to **GFS** 10 m winds (`gfs_ver = v16.3` in the v1.5.10 `run.ver`).
- **Ice forcing:** NCEP sea ice analysis (`seaice_analysis_ver = v4.5`), relevant to the Alaska domains. The rules applied (thresholds, masking) are not documented (TBD).
- **Current forcing:** One-way, from one of two sources:
  - **RTOFS-Global** surface currents ([RTOFS](../../../ocean_models/global/us/rtofs-global.md), `rtofs_ver = v2.5`), staged from the 00Z run at 3-hourly steps for all 36 offices.
  - **STOFS currents** for **Anchorage, Eureka, Portland OR and Seattle** (live-verified: only these four have `stofs_current_domain.txt`).
- **Water level:** **STOFS** for extratropical conditions and **[P-Surge](../../../storm_surge_models/regional/usa/psurge.md)** for tropical conditions, entering as a time-varying depth.
  - The STOFS water levels and currents are staged **four times daily from −6 to +180 h**, which matches [STOFS-2D-Global](../../../storm_surge_models/global/usa/stofs-2d-global.md) rather than the once-daily 3-D components (`stofs_ver = v3.1`).
  - P-Surge forcing is staged only when P-Surge is running; none was present 14–18 November 2026.
- **Nested inside:** Wave boundary conditions come from NCEP's global **WAVEWATCH III** multi-grid, i.e. [GFS-Wave](../../global/usa/ww3-noaa.md). The nested grids CG2–CG5 take boundary conditions from CG1 only (non-telescopic nesting).
- **Staged forcing is published.** NOMADS `ofs.YYYYMMDD/{rtofs,stofs}/<wfo>_output/` holds the extracted currents and water levels per office as binary `.dat` files with domain and start-time descriptors, plus per-cycle `.tar` bundles. These are inputs, not model output.

---

## Data assimilation
- **Assimilates wave observations:** No wave data assimilation is documented. NWPS is a forced system: forecaster winds, WW3 boundaries, and STOFS/RTOFS/P-Surge water level and currents.

---

## What it provides
Gridded (GRIB2, all output grids, hourly to 144 h):
- significant wave height (`HTSGW`, 10/0/3)
- primary wave direction and mean period (`DIRPW`, `PERPW`)
- significant height of total swell (`SWELL`, 10/0/8)
- wind speed and direction (the forcing winds actually used, 0/2/0–1)
- current speed and direction (10/1/0–1)
- water level (10/3/1, sea surface height)

Wave-system tracking (CG0 GRIB2): swell height, direction and mean period for up to three tracked wave systems, encoded with first fixed surface type **241** (ordered sequence) and level values 1–3 rather than as separate parameters.

Coastal hazard fields (CG1 GRIB2, at offices marked "rip + runup" above; rip probability only at Guam, Honolulu, Key West and San Juan):
- rip current occurrence probability (`RIPCOP`, 10/1/4)
- total water level with tide, wind and waves (`TWLWAV`, 10/3/205)
- runup, setup and swash (`RUNUP`, `SETUP`, `SWASH`, 10/3/206–208)
- total water level above dune toe and dune crest (`TWLDT`, `TWLDC`, 10/3/209–210)
- erosion and overwash occurrence probability (`EROSNP`, `OWASHP`, 10/3/252–253)

Point output (text):
- **Rip current probability** along the 5 m depth contour (`5m_cg1_ripprob`), hourly to 144 h: probability, wave height, peak period, wave direction relative to shore normal, tide, and an event flag
- **Runup** at shoreline points projected from the 20 m contour (`20m_cg1_runup`): wave height, period, beach slope, total water level and runup with 5th/95th percentile bounds, setup, swash and incident swash. Elevations are "relative to MSL" per the file header.
- **2-D wave spectra** (`spc2d_<station>_cg1`), SWAN standard spectral files at buoys and named coastal points (37 frequencies from 0.035 Hz)

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). Distributed via NOAA/NCEP; NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (gridded output, one file per grid per run with all hours), plain text (rip probability, runup, SWAN spectra)
- **File naming:** `nwps.tCCz.<grid>.<wfo>.grib2` (`cg0_trkng`, `cg1` … `cg5`), `nwps.tCCz.5m_cg1_ripprob.<wfo>.txt`, `nwps.tCCz.20m_cg1_runup.<wfo>.txt`, `nwps.tCCz.spc2d_<station>_cg1.<wfo>.txt`, in `<region>.YYYYMMDD/<wfo>/CC/CG<n>/`. These all-lowercase names date from v1.5. Before 9 November 2026, gridded files were named `<wfo>_nwps_<CGn>_YYYYMMDD_CC00.grib2`.
- **Official download location:**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/nwps/prod/
  (`nwps/v1.5/` is a version-named alias of the same tree)
- **Retention:** About **5 days** on NOMADS (14/15–19 November 2026 at the time of checking).
- **No cloud archive.** NWPS is not on the NODD AWS, Google or Azure mirrors (live-checked: no matching S3 or GCS bucket). Like [GLWU](./glwu.md), it is NOMADS-only, so history must be harvested as it is produced. The development feed on NOAA's polar server ended with v1.5, and the NOMADS parallel feed `nwps/para/` now returns HTTP 403.

---

## Relationship to other entries
- **Parent:** [GFS-Wave](../../global/usa/ww3-noaa.md) supplies the WAVEWATCH III wave boundary conditions for every domain.
- **Water level and current inputs:** [STOFS-2D-Global](../../../storm_surge_models/global/usa/stofs-2d-global.md) (water level everywhere; currents at four Pacific/Alaska offices), [P-Surge](../../../storm_surge_models/regional/usa/psurge.md) (tropical water level) and [RTOFS](../../../ocean_models/global/us/rtofs-global.md) (surface currents).
- **Which to use when:** NWPS is the high-resolution choice inside individual office waters; GFS-Wave covers the open ocean and the gaps between domains. The Great Lakes are covered by [GLWU](./glwu.md), not NWPS.

---

## Notes
- **On-demand runs make availability irregular.** Each office decides when to run, so there is no guaranteed cycle per domain. Absence of a cycle on NOMADS is normal, not an outage. Look for the newest `CC/` directory under each office.
- **The forcing winds are human-edited.** Because they come from the forecaster's AWIPS grids, NWPS reflects the office's official wind forecast, including its edits and any differences between neighbouring offices. Neighbouring domains are not blended, so wave fields can disagree across office boundaries. The winds actually used are carried in every CG1–CG5 file.
- **Unstructured-mesh results are interpolated.** For most offices, what NOMADS publishes is the native unstructured-mesh solution interpolated onto the regular CG1–CG5 grids for AWIPS. With v1.5 the separate nested-grid model runs were discontinued for LOX, MTR, EKA, MFR, PQR, AJK, AER, AFG and LIX: their CG2–CG5 files are now interpolations from the mesh, and their nested-grid spectra were dropped. Live-verified: Seattle is the only office still publishing CG2/CG3 spectral files.
- **Grid changes at v1.5:**
  - Eureka gained a CG2 over the Humboldt Bay spit (about 50 m output spacing).
  - The Grays Harbor bar nest moved from Portland OR to Seattle's CG2 (live-verified at 46.84–47.01°N). Portland OR still publishes a CG2, now over 46.59–46.79°N.
  - Eureka and Seattle publish a separate wind-only run as CG3.
- **Rip current and runup coverage grew sharply at v1.5.** 27 offices now publish runup and 31 rip probability (live-verified). v1.5 added 11 West Coast and western Gulf offices (LOX, MTR, EKA, MFR, PQR, SEW, SGX, BRO, LCH, LIX, CRP). Guam, Honolulu, Key West and San Juan have rip probability only. The four Alaska offices and Baltimore/Washington (Chesapeake Bay) have neither. NOAA's EMC page still describes this guidance as experimental at "5 pilot WFOs", which is out of date.
- **ecCodes cannot name the hazard fields.** As of ecCodes 2.50, the 10/3/205–210 and 10/3/252–253 parameters (NCEP local table) and 10/1/4 `RIPCOP` all decode as `unknown`. Select on discipline/category/number using the NCEP GRIB2 tables (see *Official documentation*). Messages use simple packing, carry a bitmap, and declare `missingValue = 9999`.
- **The v1.5 notice's file names are not quite what is published.** The SCN gives the tracking file as `nwps.tCCz.cg0.trkng.<wfo>.grib2` and the spectra as `nwps.tCC.spc2d_…`. Live names are `nwps.tCCz.cg0_trkng.<wfo>.grib2` and `nwps.tCCz.spc2d_…`. Its header is also dated "Oct 6 2023", which is a typo for 2026.
- **Version strings:** the operational package is `nwps.v1.5.10`, built from `nwps_ver = v1.5.0`. Its top-level release notes file still describes v1.3.0, but older notes for v1.3.9–v1.4.x are kept alongside it.

---

## Recent version history
- **v1.5 — 9 November 2026** (SCN 26-91):
  - **Hazard guidance:** rip and runup guidance at 11 more offices.
  - **Meshes:** 12 more offices moved to unstructured meshes; Seattle moved to a finer structured grid.
  - **Nested grids:** new Eureka Humboldt Bay CG2; Grays Harbor nest moved to Seattle; wind-only CG3 at Eureka and Seattle.
  - **Distribution:** all-lowercase NCO-standard file names; `estofs` staging renamed `stofs`; nested-grid runs and spectra discontinued at nine offices; polar.ncep.noaa.gov distribution ended.
- **v1.4.20 — 22 September 2025** (SCN 25-66): rip probability, runup and spectral point files moved from polar.ncep.noaa.gov to NOMADS.
- **v1.3 — 3 February 2021** (SCN 20-116):
  - **Wave tracking:** improved wave-system tracking at all 36 offices, with the low-frequency cut-off lowered to 0.035 Hz.
  - **Meshes:** unstructured meshes (5 km–200 m) at 12 more offices.
  - **Hazard guidance:** first rip current and runup (erosion/overwash) guidance at 9 offices, plus rip-only at Key West and Guam.
  - **Water level:** ESTOFS water level inputs.
- **v1.2 — 17 January 2018** (SCN 17-84): forecast extended from 102 h to 144 h; output from 3-hourly to hourly; unstructured meshes at 10 offices; GFS wind fail-over added.
- **Implementation:** NWPS entered NCEP's production suite in 2016 (exact date TBD).

---

## Official documentation
- SCN 26-91 — NWPS v1.5: https://www.weather.gov/media/notification/pdf_2026/scn26-91_nwps_1.5.0.pdf
- SCN 25-66 — NWPS v1.4.20 (rip/runup/spectra to NOMADS): https://www.weather.gov/media/notification/pdf_2025/scn25-66_nwps_1.4.20.pdf
- SCN 20-116 (Updated) — NWPS v1.3: https://www.weather.gov/media/notification/pdf2/scn20-116nwps_v1.3_aab.pdf
- SCN 17-84 (Updated) — NWPS v1.2: https://weather.gov/media/notification/pdfs/scn17-84nwps_upgrade_aae.pdf
- NWPS v1.5 WMO headers: https://www.nco.ncep.noaa.gov/pmb/changes/docs/wmo_nwpsv1_5.txt
- Operational code and configuration (domains, versions, release notes): https://www.nco.ncep.noaa.gov/pmb/codes/nwprod/nwps.v1.5.10/
- EMC NWPS page: https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/nwps.php
- NWPS page and model viewer (polar server): https://polar.ncep.noaa.gov/nwps
- NCEP GRIB2 tables 4.2-10-1 and 4.2-10-3 (hazard parameters): https://www.nco.ncep.noaa.gov/pmb/docs/grib2/grib2_doc/grib2_table4-2-10-3.shtml
- NOMADS: https://nomads.ncep.noaa.gov/pub/data/nccf/com/nwps/prod/
