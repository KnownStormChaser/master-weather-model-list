# GTGN (Graphical Turbulence Guidance Nowcast)

## What this model is
The Graphical Turbulence Guidance Nowcast (GTGN) is a NOAA/NCEP aviation turbulence nowcasting product that provides a near-real-time analysis of in-flight turbulence, updated every 15 minutes. It takes a short-term (1–2 hour) forecast from the Graphical Turbulence Guidance (GTG) as a background field and blends in recent turbulence observations to produce an updated, observation-consistent turbulence analysis. Its output variable is the Eddy Dissipation Rate (EDR), an aircraft-independent metric of turbulence intensity. GTGN is explicitly not a substitute for forecaster-issued turbulence SIGMETs.

Unlike most nowcasting products (which are 2D surface fields), GTGN is a genuinely three-dimensional field, output through the depth of the aviation flight envelope.

---

## Who runs it
- **Organization:** NOAA / NCEP — Aviation Weather Center (AWC), with data flow via NWS/NCEP Central Operations. Algorithm developed at NCAR/RAL under FAA sponsorship; transitioned to operations through the Aviation Weather Testbed with CIRA (Colorado State University).
- **Country / region:** United States

---

## What area it covers
- **Coverage:** CONUS
- **Domain details:** Follows the 3 km HRRR CONUS domain (the GTG forecast that serves as GTGN's background is based on the 3 km HRRR, so the HRRR domain defines the GTGN horizontal domain)

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** Seamless blend of a short-range NWP-derived forecast (GTG) with turbulence observations, producing a near-real-time updated analysis
- **Technique / algorithm:** A 1–2 h GTG forecast is used as the background/basis; recent turbulence observations are then merged in on a point-by-point basis to update the field into a blended analysis
- **Underlying / driving model:** GTG (Graphical Turbulence Guidance), which is itself based on NOAA's 3 km [HRRR](../../../nwp_models/regional/usa/hrrr.md) over CONUS. See also [DAFS](../../../nwp_models/regional/usa/dafs.md), which is the operational NCEP system producing 3 km HRRR-derived GTG v4.0 over CONUS. [TBD: public documentation does not state whether GTGN ingests the DAFS `com/dafs/prod` GTG stream directly or a separately-run NCAR-side GTG; the two are the same algorithm at the same resolution over the same domain, but the provenance is unconfirmed.]
- **Probabilistic / ensemble:** No (deterministic EDR field)
- **Horizontal resolution:** 3 km
- **Vertical structure:** 3D — 51 levels: 100 ft, then every 1,000 ft from FL010 to FL500 (i.e. 1,000 ft to 50,000 ft)
- **Lead time:** Near-real-time analysis, updated every 15 min, anchored to a 1–2 h GTG forecast background. Files are stamped at their 15-min valid time (`t0000z`, `t0015z`, …) with no forecast-hour (`fHH`) suffix, suggesting each file is a single field valid at its timestamp rather than a bundle of lead times. [Verify against GRIB2 message contents with `wgrib2` to confirm whether any forward projection is embedded.]
- **Update frequency:** Every 15 minutes
- **Temporal output resolution:** 15 min
- **Latency:** ~1–2 min after valid time on the operational feed (inferred from server file-write times — e.g. `t0200z` posted at 02:02, `t0215z` at 02:17, `t0230z` at 02:32, `t0245z` at 02:46, observed 7 Aug 2026; not an official spec). This is faster than the pre-operational `v1.0`/`para` streams, which ran ~3–4 min behind valid time.

---

## Inputs
- **Radar:** EDR estimated from ground-based radar via the NCAR Turbulence Detection Algorithm (NTDA)
- **Lightning:** EDR estimated from ground-based lightning data
- **Satellite:** Satellite-derived turbulence inferences
- **Surface / other observations:** METAR data; aircraft observations from pilot reports (PIREPs); automated in-situ eddy dissipation rate (EDR) reports from commercial aircraft
- **NWP fields:** GTG short-range (1–2 h) forecast as the background field (GTG is HRRR-based)

---

## What it provides
- Eddy Dissipation Rate (EDR) — a 3D, aircraft-independent turbulence field, representing maximum expected turbulence across sources (clear-air turbulence, mountain-wave turbulence, and convectively induced turbulence)
- GRIB2 parameter: Eddy Dissipation Parameter (EDPARM), Category 19, Parameter 30

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work; CC0-equivalent)
- **Is the data downloadable?** Yes (a web display also exists at AviationWeather.gov/gfa/#obs, but raw GRIB2 is separately available — in scope)
- **Data formats:** GRIB2 (JPEG2000 compression with bitmap), 3 km; ~30 MB per file
- **File naming / structure:** `gtgn.YYYYMMDD/HH/gtgn.tHHMMz.3km.grib2` — where `HH` is the hour bin (`00`–`23`), and each hour bin holds four files at `t{HH}00z`, `t{HH}15z`, `t{HH}30z`, `t{HH}45z` (the 15-min cadence). Example: `gtgn.20260807/02/gtgn.t0200z.3km.grib2`
- **Official download location:**
  - https://nomads.ncep.noaa.gov/pub/data/nccf/com/gtgn/prod/
- **Access route notes:**
  - NOMADS appears to be the sole open distribution channel; no AWS Open Data / NODD mirror has been announced. Same situation as [DAFS](../../../nwp_models/regional/usa/dafs.md).
  - **Retention is short** — only two date directories are kept at a time (a rolling window of roughly one to two days, observed 7 Aug 2026). Users needing any archive must capture files in near-real-time.
  - **Discoverability caveat:** on the NOMADS index page, GTGN is listed under **"Global Models"**, not "Regional Models". This is a NOMADS-side filing artifact — the Global list is ordered alphabetically and "GTGN" happens to sort among the GFS-family entries. GTGN is CONUS-only (see *What area it covers*); DAFS, which is likewise HRRR-derived, is correctly filed under Regional Models on the same page.
  - [The pre-operational `v1.0` and `para` streams were confirmed live and mirrored on 6 Jul 2026. Whether either persists as an alias alongside `prod` post-implementation is **unverified** — re-check `https://nomads.ncep.noaa.gov/pub/data/nccf/com/gtgn/` for surviving sibling directories.]

---

## Notes
- **Status:** **Operational.** The `/prod/` feed is live and serving 15-minute GRIB2 files (verified 7 Aug 2026). [Date conflict: SCN 26-62 specified implementation "on or about 28 July 2026, 1200 UTC" and was never superseded or updated, but the NWS news release of 3 Aug 2026 states the AWC debuted GTGN on **30 July 2026**. The two-day gap is consistent with the SCN's own slip provision (implementation moves to the next suitable weekday if a Critical Weather Day / Enhanced Caution Event is declared), but this has not been confirmed, and the news item may alternatively refer to the AviationWeather.gov display rather than the NCEP data implementation.]
- **Parent product:** GTG (Graphical Turbulence Guidance) is the longer-range forecast product GTGN is built on. Operational 3 km GTG v4.0 is distributed as part of [DAFS](../../../nwp_models/regional/usa/dafs.md), which has its own entry; GTG therefore does not need a separate entry, but the GTGN↔DAFS provenance question above is worth resolving.
- GTGN is a rare 3D nowcast and a rare turbulence-specific nowcast — most catalog nowcasts are surface precipitation. Its EDR output is aviation-focused.
- **Domain cross-check:** the observed ~30 MB file size is itself strong evidence for the CONUS domain. On the HRRR CONUS grid (1799 × 1059) at 51 levels this is ~2.6 bits per value, which is plausible for JPEG2000-with-bitmap on a largely quiet field. A global 3 km grid at the same levels would require ~0.06 bits per value, which is not achievable.

---

## Recent version history
### Version 1.0 — operational 30 July 2026 (SCN 26-62)
- First operational implementation of GTGN at NCEP; product debuted at the Aviation Weather Center on 30 July 2026.
- SCN 26-62 (issued 25 June 2026) scheduled implementation for on or about 28 July 2026, 1200 UTC.
- Originally scheduled for 29 June 2026 under SCN 26-46, which was **rescinded** and replaced by SCN 26-62.
- Preceded by an experimental phase / public comment period (PNS 26-08).
- Predecessor generation: GTGN1 was disseminated semi-operationally by NCAR, built on a 13 km RAP-based GTG3 background. The operational v1.0 corresponds to the GTGN2 line of development — 3 km throughout (GTG4 background, NTDA ingested nearer its native resolution) with lightning data added as a turbulence inference source.

---

## Official documentation
- NWS Service Change Notice 26-62 (implementation): https://www.weather.gov/media/notification/pdf_2026/scn26-62_GTGN_v1.0.pdf
- NWS Service Change Notice 26-46 (earlier implementation notice; rescinded): https://www.weather.gov/media/notification/pdf_2026/scn26-46_gtgn_implementation.pdf
- NWS Public Information Statement 26-08 (experimental GTGN comment period): https://www.weather.gov/media/notification/pdf_2026/pns26-08_ExpGTGN.pdf
- NWS news release, 3 Aug 2026 ("New turbulence detection tool aims to make commercial flight smoother"): https://www.weather.gov/news/260803-gtgn
- FAA Aviation Weather Research Program — Turbulence: https://www.faa.gov/nextgen/programs/weather/awrp/turbulence
- NCAR/RAL product page: https://ral.ucar.edu/solutions/products/graphical-turbulence-guidance-nowcast-gtgn
- Display: https://aviationweather.gov/gfa/#obs
