# HYSPLIT-Dust (NAQFC Dust Forecast)

## What this model is
HYSPLIT-Dust is the operational atmospheric dust forecast component of NOAA/NWS's National Air Quality Forecast Capability (NAQFC). It uses the Hybrid Single-Particle Lagrangian Integrated Trajectory model (HYSPLIT), a widely used Lagrangian transport and dispersion model developed by NOAA's Air Resources Laboratory (ARL). Here HYSPLIT is configured for windblown dust transport and deposition over the contiguous United States.

This entry documents HYSPLIT only in its role as the operational NAQFC dust component. HYSPLIT is used far more broadly, internationally, for emergency response (volcanic ash, nuclear and hazmat releases), backward trajectory analysis for source attribution, and many other dispersion applications. Those uses are outside this repository's operational-forecast scope.

HYSPLIT-Dust is one of three component prediction systems making up NAQFC, alongside [AQM](./aqm.md) (operational ozone and PM2.5 forecasts) and [RAP](../../../nwp_models/regional/usa/rap.md) (operational smoke forecasts).

Since **HYSPLIT 9.1** (SCN 26-78, effective November 3, 2026) the meteorology driving it comes from [RRFS](../../../nwp_models/regional/usa/rrfs.md) instead of the retired NAM. See [Meteorological driver](#meteorological-driver).

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP), with model development at NOAA's Air Resources Laboratory (ARL)
- **Country / region:** United States
- **Programme:** Component of the National Air Quality Forecast Capability (NAQFC), a joint NOAA-EPA programme

---

## What area it covers
- **Coverage:** Contiguous United States (CONUS) only
- **Note:** Dust forecasts are not produced for Alaska or Hawaii. Both receive smoke (from RAP) and ozone/PM2.5 (from AQM), but dust forecasting is CONUS-only under the current NAQFC configuration.

---

## Basic details
- **Model type:** Lagrangian atmospheric transport and dispersion (windblown dust)
- **Core model:** HYSPLIT (Hybrid Single-Particle Lagrangian Integrated Trajectory model), operational version 9.1
- **Forecast length:** 48 hours
- **Update frequency:** 2× daily (06, 12 UTC)
- **Product timing:** On 2026-11-03 the 06 UTC files landed at 09:09 UTC and the 12 UTC files at 15:45 UTC. Per SCN 26-78, the RRFS input makes them **~11–15 minutes later at 06 UTC and ~45–50 minutes later at 12 UTC** than under the NAM-driven 9.0. Fixed-schedule pollers need to allow for this.
- **Temporal output resolution:** Hourly — 48 one-hour average windows (0–1 h to 47–48 h) per file
- **Horizontal grids:**
  - Native ~0.1° regular lat/lon (601 × 251)
  - NCEP Grid 227 (Lambert, 1473 × 1025 at ~5 km over CONUS), the `_227.grib2` variant
- **Output units:** log10(µg/m³), i.e. fine-particulate (dust) mass concentration on a base-10 log scale (e.g. ~2.7 ≈ 500 µg/m³). The value −99 is the below-threshold fill.
- **Output products:**
  - `sfc` — near-surface layer dust (0–100 m above ground), hourly
  - `pbl` — deep-layer dust (0–5000 m above ground), hourly

---

## Meteorological driver
HYSPLIT is an offline-coupled dispersion model. It ingests pre-computed meteorological fields from a separate NWP run, in NOAA's ARL-packed format, rather than computing meteorology itself. The driving NWP source is therefore a configuration choice that follows the operational suite.

**Since HYSPLIT 9.1 the driver is RRFS.** The ARL-packed inputs on NOMADS under `hysplit/prod/hysplit.YYYYMMDD/` are, for the 00/06/12/18 UTC cycles (verified 2026-11-03):

| Input file | Domains |
|---|---|
| `hysplit.tCCz.rrfs.${domain}` | `na`, `hi`, `pr`, `firewx` |
| `hysplit.tCCz.rrfsfHH.${domain}` (HH = 00, 06, 12, 18, 24, 30, 36, 42) | `ak`, `conus` |
| `hysplit.tCCz.rrfsa.${domain}` (archived) | `na`, `ak`, `hi`, `pr` |

These replaced the NAM-derived `hysplit.tCCz.nam*` set and the regional-ensemble inputs `hysplit.tCCz.rens.{nam,hiresw,hrrr}.m0M`. NAM and HREF were retired the same day under SCN 26-47.

- **The switch was early.** The RRFS files are present from the **12 UTC cycle on November 2, 2026**, a day before the SCN's effective date. No NAM or `rens` file remains in either of the two days NOMADS retains.
- **The SCN's filename lists were right; its prose was not.** The prose lists `na, hi, ak, pr, firewx` for the RRFS forecast files, but the live files match the filename lines: Alaska is carried only through `rrfsfHH` and the archive file.
- **The input volume is larger.** SCN 26-78 notes that RRFS's higher spatial and vertical resolution increases data volume throughout, which is the cause of the later product times above.

---

## Dust emissions
Windblown dust emissions for HYSPLIT-Dust are computed using a wind-driven erosion scheme that depends on:
- Surface wind speed
- Soil characteristics (texture, moisture)
- Vegetation cover (greenness vegetation fraction)
- Threshold friction velocities by soil type

The dust scheme is most active over arid and semi-arid regions of the western and southwestern United States. There, dust events from playas, deserts and disturbed land surfaces contribute meaningfully to PM10 and PM2.5 air quality during wind events.

---

## What it provides
- Hourly near-surface dust field (`sfc`) over the CONUS
- Hourly deep-layer (0–5 km) dust field (`pbl`)
- Both distributed as log10(µg/m³) on a native ~0.1° lat/lon grid and on NCEP Grid 227 (~5 km CONUS)
- Forecast horizon out to 48 hours from each cycle

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data format:** GRIB2
- **Primary access (live) — NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/hysplit/prod/
  - Dust output lives in per-day `dustcs.YYYYMMDD/` directories, for the 06 and 12 UTC cycles:
    - `dustcs.tCCz.sfc.1hr.grib2` (surface)
    - `dustcs.tCCz.pbl.1hr.grib2` (deep layer)
    - Each also on NCEP Grid 227 (~5 km CONUS), as the `_227.grib2` variant.
  - Two days are retained. Directory listings need `curl --http1.1` (see the [RRFS entry](../../../nwp_models/regional/usa/rrfs.md#data-availability)).
- **Historical archive — AWS Open Data (NODD):** `s3://noaa-nws-naqfc-pds/HYSPLIT_Dust/` (browse: https://noaa-nws-naqfc-pds.s3.amazonaws.com/index.html). **Not a live mirror; use NOMADS for current data.** Re-verified on 2026-11-03 by full object listing:
  - **Continuous coverage ends in April.** It runs from 2020-01-01 to the 06 UTC cycle of 2026-04-20, which was written in near real time.
  - **A single bulk backfill added later dates, with gaps.** On **2026-10-08** (around 18:00 UTC), files from 2026-04-20 to the 12 UTC cycle of 2026-10-06 were uploaded. Only about 58% of the expected files are there: 21 days are missing entirely and roughly 100 carry only one of the two cycles.
  - **Nothing has been added since.** For the period from April 20, check the cycle you need before relying on it.

### Archive naming differs from NOMADS
The S3 archive does **not** use the NOMADS filenames, and it carries a narrower product set. Files live under `HYSPLIT_Dust/CS/YYYYMMDD/{06,12}/`:

| NOMADS | S3 archive |
|---|---|
| `dustcs.tCCz.sfc.1hr_227.grib2` | `hsp.tCCz.ave_1hr_SfcDust.YYYYMMDD.227.grib2` |
| `dustcs.tCCz.pbl.1hr_227.grib2` | `hsp.tCCz.ave_1hr_ColDust.YYYYMMDD.227.grib2` |
| `dustcs.tCCz.{sfc,pbl}.1hr.grib2` (native ~0.1° lat/lon) | *not archived* |

The archive holds four files per day (two cycles by two products) on **Grid 227 only**. The native ~0.1° lat/lon variant exists on NOMADS but has no archived counterpart, so any long-run analysis on the native grid has to be built from a live capture. The archive also names the deep-layer product `ColDust` (column dust) where NOMADS calls it `pbl`; they are the same field.
- **Operational forecast viewer:** https://airquality.weather.gov/

---

## Notes
- **Grid 227 files carry a bitmap.** Points outside the native dust domain are masked, and ecCodes returns them as its missing value (9999), separate from the −99 below-threshold fill. On a 2026-11-03 12 UTC surface file, roughly 60% of Grid 227 is masked, 34% is −99 and 6% carries a dust value. Treat both 9999 and −99 as "no dust" when aggregating.
- **Encoding:** GRIB2, centre `kwbc`, `generatingProcessIdentifier` 6. Parameter discipline 0 / category 13 / number 195 (NCEP local table), which ecCodes names `lipmf`, on `heightAboveGroundLayer`: 0–100 m (`sfc`) or 0–5000 m (`pbl`). Grids, layers, units and the 48-window structure match the pre-upgrade description, but no pre-upgrade file is left on NOMADS to compare encodings record for record.
- **The November 2, 2026 dust output is not on NOMADS.** On November 3 the listing holds `dustcs.20261103/` and an empty `dustcs.20261101/`, but no `dustcs.20261102/`. That day coincides with the early switch to RRFS inputs. Whether the Nov 2 dust cycles ran is unknown (TBD), and they are not in the S3 archive either.
- **Disregard the SCN 26-78 CMAQ v4 line.** The notice closes by saying the changes "will only be applicable to CONUS results" and that "Alaska and Hawaii will continue using CMAQ v4". CMAQ is the [AQM](./aqm.md) component of NAQFC, not HYSPLIT, and no CMAQ v4 appears anywhere in the current operational NAQFC chain. It reads as boilerplate carried over from an air-quality notice. HYSPLIT-Dust is CONUS-only regardless.
- HYSPLIT-Dust is the only one of the three NAQFC component models that does **not** cover Alaska or Hawaii, and the first NAQFC component to consume RRFS output.
- This entry intentionally focuses on the operational NAQFC dust application. HYSPLIT in its broader form (run interactively via the READY system, and used for emergency response, backward trajectory analysis and other non-NAQFC applications) is outside this repository's scope. For those uses, see the ARL HYSPLIT page under *Official documentation*.

---

## Recent version history
- **HYSPLIT 9.1 — effective 2026-11-03 (RRFS inputs present from 2026-11-02, 12 UTC).** NWS SCN 26-78 (September 9, 2026; AAA update of October 2, 2026, which moved the date from October 14):
  - RRFS coupled in as the meteorological driver.
  - The NAM and HREF couplings retired, including the `rens` ensemble inputs.
  - The dust products arrive later.
- **2022-06-28 — smoke role ends.** RAP-Smoke replaced HYSPLIT for operational NAQFC smoke prediction (see [RAP](../../../nwp_models/regional/usa/rap.md)). HYSPLIT has since been used within NAQFC solely for dust.

---

## Official documentation
- AWS Open Data Registry entry: https://registry.opendata.aws/noaa-nws-naqfc-pds/
- NCEP/EMC AQM change log (covers HYSPLIT-Dust as a NAQFC component): https://www.emc.ncep.noaa.gov/mmb/aq/AQChangelog.html
- NWS SCN 26-78, AAA update of October 2, 2026 (HYSPLIT 9.1: RRFS coupling in, NAM and HREF couplings retired; effective November 3, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26_78_Updated_HYSPLIT_%20v9.1_aaa.pdf
- NOAA Air Resources Laboratory HYSPLIT page: https://www.ready.noaa.gov/HYSPLIT.php
- NOAA OSTI Air Quality program page: https://vlab.noaa.gov/web/osti-modeling/air-quality
- NWS Air Quality Forecast Guidance viewer: https://airquality.weather.gov/
- NCEP AQM Products website: https://www.emc.ncep.noaa.gov/mmb/aq/
