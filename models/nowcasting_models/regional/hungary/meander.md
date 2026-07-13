# MEANDER

## What this model is
MEANDER (MEsoscale ANalysis and DEcision Routines) is HungaroMet's operational analysis and ultra-short-term forecasting (nowcasting) system. It produces a gridded analysis of current surface weather and 0–2 hour nowcasts over Hungary by blending WRF NWP output with surface measurements, weather-radar data, and satellite information, refreshing every 10 minutes. It is intended for tracking ongoing weather, extracting local weather data, and supporting weather warnings.

---

## Who runs it
- **Organization:** HungaroMet (Hungarian Meteorological Service, formerly OMSZ — Országos Meteorológiai Szolgálat)
- **Country / region:** Hungary

---

## What area it covers
- **Coverage:** Hungary and the surrounding Carpathian Basin
- **Domain details:** **355 (W–E) × 220 (N–S)** grid points (global attrs `Nx=355`, `Ny=220`). Northwest (first) grid point at **lat 48.8°N, lon 15.7°E** (`La1`/`Lo1`), with north→south row order; grid spacing **dx = 0.021274°** (E–W), **dy = 0.014378°** (N–S). This yields an extent of ≈ **45.65°N–48.8°N, 15.7°E–23.2°E** (south/east edges derived from the corner + spacing × dimensions). Read directly from the distributed NetCDF global attributes.
- **Projection:** Regular latitude–longitude (spherical)

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** Seamless extrapolation–NWP blend — a multi-source objective analysis combined with radar-based extrapolation and WRF NWP fields
- **Technique / algorithm:** Grid-point analysis of surface, radar, and satellite observations; precipitation nowcasting via advection (extrapolation) of radar signals; blended with WRF forecast values (detailed weighting/crossover not documented — TBD)
- **Underlying / driving model:** WRF (HungaroMet's WRF configuration; not the agency's operational AROME/ALARO suite — see *Notes*)
- **Probabilistic / ensemble:** No (deterministic)
- **Horizontal resolution:** ~1.6 km (0.021274° × 0.014378°)
- **Vertical structure:** 2D single-level surface fields
- **Lead time:** 0–2 hours
- **Update frequency:** Every 10 minutes
- **Temporal output resolution:** 10 minutes
- **Latency:** TBD

---

## Inputs
- **Radar:** HungaroMet national radar network — **five C-band (5.5 cm) dual-polarisation Doppler radars** (Budapest, Napkor, Pogányvár, Szentes, Hármas-hegy), 240 km range, ingested via the national reflectivity composite (CMax / PseudoCAPPI); radar reflectivity/signal (dBZ) and radar-signal advection drive the precipitation extrapolation. See [HungaroMet Radar](../../../../observations/radar/hungary/hungaromet-radar.md).
- **Satellite:** Yes (satellite information used in the analysis; channels TBD)
- **Surface / other observations:** Surface station measurements
- **NWP fields:** WRF forecast values

---

## Blending / seamless transition
MEANDER merges radar-based extrapolation with WRF NWP fields across the short 0–2 h window. The specific blending weights and crossover behaviour are not documented in the dataset description. (TBD)

---

## What it provides
Gridded surface analysis/nowcast fields (one variable per file):

| Variable | Description | Unit |
|---|---|---|
| T2 | 2 m temperature | K |
| U10 | 10 m wind, west–east component | m/s |
| V10 | 10 m wind, south–north component | m/s |
| PSFC | Surface pressure | Pa |
| PSEALEVEL | Mean sea-level pressure | hPa |
| cloudines | Cloud cover | okta |
| maxRadSig | Radar signal | dB |
| sumRadPrec | Predicted precipitation from radar-signal advection | mm |
| sumPrec_01hour | Precipitation in the last 1 hour (radar + surface measurements) | mm |
| WGUST | Wind gust | m/s |
| Visibility | Visibility | m |
| presWeather | Present-weather code (categorical) | — |
| simpleWeather | Simplified present-weather code (categorical) | — |

`presWeather` and `simpleWeather` are categorical coded fields; the dataset description PDF gives the full code → meaning lookup tables (e.g. `presWeather` spans clear-sky, fog, graded gale/storm, blowing-snow, freezing-rain, and graded thunderstorm categories; `simpleWeather` is a coarser 1–24 scheme).

---

## Data availability
- **Is the data free?** Yes
- **License:** HungaroMet Open Data Portal terms (free use without modification; attribution required as *"Database: Meteorological Database, HungaroMet Nonprofit Zrt."*; modifications require written consent, with example attribution forms provided in the General Terms of Use). Warnings, alerts, and aviation forecasts may only be redistributed unmodified.
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (one variable per file, compressed inside ZIP archives)
- **Official download location:**
  https://odp.met.hu/weather/nwp/MEANDER/nc/
  - Organized into 24 hourly subfolders (`00/`–`23/`), one per UTC hour of the run
- **File naming convention** (from the dataset description):
  - `MEANDER-<variable>-<YYYYMMDD>_<HHmm>+<TTTtt>.nc.zip`
  - `<variable>`: meteorological variable
  - `<YYYYMMDD>`: forecast run date
  - `<HHmm>`: forecast initial time (UTC)
  - `<TTTtt>`: forecast lead time, hours (TTT) and minutes (tt)

---

## Notes
- **Operational since 2005**, with version changes roughly once a year (no detailed public changelog).
- **Distinct from HungaroMet's NWP suite:** MEANDER is driven by WRF, separate from the agency's operational [AROME](../../../nwp_models/regional/hungary/arome-hungary.md) and ALARO models. The 10-minute refresh and 0–2 h lead time make it a true nowcasting product rather than an NWP run.
- **Uncertainties:** Noisy radar measurements, damaged or incorrect surface/satellite data, or a temporary deficit of certain data types can distort the analysis and propagate into the forecast.
- **Coded weather fields:** Full `presWeather` / `simpleWeather` code tables are in the dataset description PDF; they can be embedded here as a reference appendix if you want them inline. In the distributed files both carry `units = "category"`.
- **Mixed pressure units:** `PSFC` is in `Pa` but `PSEALEVEL` is in `hPa` (confirmed from the files) — don't assume both are Pa.
- **Geolocation gotcha:** Files declare `conventions = "LFS"` with no CF coordinate variables or `grid_mapping`; build the lat/lon axes from `La1`/`Lo1` + `Nx`/`Ny`/`Dx`/`Dy`, and note the **north→south** row order (first grid point is the NW corner) before plotting — same pattern as the [HungaroMet radar composites](../../../../observations/radar/hungary/hungaromet-radar.md).

---

## Recent version history
- Operating since 2005; version changes occur approximately annually. No version identifiers are published in the dataset description, though the distributed NetCDF files embed a `version` global attribute (observed value `9991.0`, likely a placeholder/test value rather than a release number — flag).

---

## Official documentation
- HungaroMet MEANDER page (Hungarian): https://www.met.hu/idojaras/elorejelzes/modellek/MEANDER/
- MEANDER dataset description (PDF, English): https://odp.met.hu/weather/nwp/MEANDER/Description_nowcasting_forecast-MEANDER-en.pdf
- HungaroMet open data portal (root): https://odp.met.hu/

### Contact
- Open data technical contact: odp@met.hu
