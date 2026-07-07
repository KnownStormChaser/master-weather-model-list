# GTGN (Graphical Turbulence Guidance Nowcast)

## What this model is
The Graphical Turbulence Guidance Nowcast (GTGN) is a NOAA/NCEP aviation turbulence nowcasting product that provides a near-real-time analysis of in-flight turbulence, updated every 15 minutes. It takes a short-term (1–2 hour) forecast from the Graphical Turbulence Guidance (GTG) as a background field and blends in recent turbulence observations to produce an updated, observation-consistent turbulence analysis. Its output variable is the Eddy Dissipation Rate (EDR), an aircraft-independent metric of turbulence intensity. GTGN is explicitly not a substitute for forecaster-issued turbulence SIGMETs.

Unlike most nowcasting products (which are 2D surface fields), GTGN is a genuinely three-dimensional field, output through the depth of the aviation flight envelope.

---

## Who runs it
- **Organization:** NOAA / NCEP — Aviation Weather Center (AWC), with data flow via NWS/NCEP Central Operations
- **Country / region:** United States

---

## What area it covers
- **Coverage:** CONUS
- **Domain details:** Follows the 3 km HRRR CONUS domain (the GTG forecast that serves as GTGN's background is based on the 3 km HRRR, so the HRRR domain defines the GTGN horizontal domain)

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** Seamless blend of a short-range NWP-derived forecast (GTG) with turbulence observations, producing a near-real-time updated analysis
- **Technique / algorithm:** A 1–2 h GTG forecast is used as the background/basis; recent turbulence observations are then merged in to update the field into a blended analysis
- **Underlying / driving model:** GTG (Graphical Turbulence Guidance), which is itself based on NOAA's 3 km HRRR over CONUS. [Cross-link to the HRRR entry in `nwp_models`.]
- **Probabilistic / ensemble:** No (deterministic EDR field)
- **Horizontal resolution:** 3 km
- **Vertical structure:** 3D — output from the surface (100 ft) and then every 1,000 ft up to 50,000 ft
- **Lead time:** Near-real-time analysis, updated every 15 min, anchored to a 1–2 h GTG forecast background. Files are stamped at their 15-min valid time (`t0000z`, `t0015z`, …) with no forecast-hour (`fHH`) suffix, suggesting each file is a single field valid at its timestamp rather than a bundle of lead times. [Verify against GRIB2 message contents with `wgrib2` to confirm whether any forward projection is embedded.]
- **Update frequency:** Every 15 minutes
- **Temporal output resolution:** 15 min
- **Latency:** ~3–4 min after valid time (inferred from server file-write times — e.g., the `t0000z` file posted at 00:04, `t0045z` at 00:49; not an official spec)

---

## Inputs
- **Radar:** EDR estimated from ground-based radar via the NCAR Turbulence Detection Algorithm (NTDA)
- **Lightning:** EDR estimated from lightning data
- **Surface / other observations:** METAR data; aircraft observations from pilot reports (PIREPs); automated in-situ eddy dissipation rate (EDR) reports
- **NWP fields:** GTG short-range (1–2 h) forecast as the background field (GTG is HRRR-based)

---

## What it provides
- Eddy Dissipation Rate (EDR) — a 3D, aircraft-independent turbulence field
- GRIB2 parameter: Eddy Dissipation Parameter (EDPARM), Category 19, Parameter 30

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work; CC0-equivalent)
- **Is the data downloadable?** Yes (a web display also exists at AviationWeather.gov/gfa/#obs, but raw GRIB2 is separately available — in scope)
- **Data formats:** GRIB2 (JPEG2000 compression with bitmap), 3 km; ~31 MB per file
- **File naming / structure:** `gtgn/<feed>/gtgn.YYYYMMDD/HH/gtgn.tHHMMz.3km.grib2` — where `<feed>` is `para`, `v1.0`, or (once operational) `prod`; `HH` is the hour bin (`00`–`23`); and each hour bin holds four files at `t{HH}00z`, `t{HH}15z`, `t{HH}30z`, `t{HH}45z` (the 15-min cadence). Example: `gtgn/v1.0/gtgn.20260706/00/gtgn.t0000z.3km.grib2`
- **Official download location(s):**
  - Version-tagged feed: https://nomads.ncep.noaa.gov/pub/data/nccf/com/gtgn/v1.0/
  - Parallel / evaluation feed (named in SCN): https://nomads.ncep.noaa.gov/pub/data/nccf/com/gtgn/para/
  - Operational feed (per SCN, from ~28 Jul 2026): https://nomads.ncep.noaa.gov/pub/data/nccf/com/gtgn/prod/
  - [Confirmed 6 Jul 2026: the `v1.0` and `para` feeds carry identical content — same filenames, 31 MB sizes, and modification times — i.e. mirrored pre-operational streams. The SCN designates `/prod/` as the operational path; re-verify at go-live (~28 Jul 2026) whether `v1.0` persists as an alias alongside `prod`.]

---

## Notes
- **Status (as of drafting):** Pre-operational, but the feed is live — as of 6 July 2026 both the `para` and `v1.0` streams are actively serving 15-minute GRIB2 files. Operational implementation is scheduled on or about **28 July 2026, 1200 UTC** (SCN 26-62), when the SCN-designated `/prod/` path is expected to become authoritative. Implementation slips to the next suitable weekday if the date is declared a Critical Weather Day / Enhanced Caution Event.
- **Parent product:** GTG (Graphical Turbulence Guidance) is the longer-range forecast product GTGN is built on; it may warrant its own entry (as a forecast/diagnostic rather than a nowcast) or a cross-reference. A related NOAA product, DAFS GTG, also exists.
- GTGN is a rare 3D nowcast and a rare turbulence-specific nowcast — most catalog nowcasts are surface precipitation. Its EDR output is aviation-focused.

---

## Recent version history
### Version 1.0 — operational on or about 28 July 2026 (SCN 26-62)
- First operational implementation of GTGN at NCEP.
- Originally scheduled for 29 June 2026 under SCN 26-46; rescheduled to 28 July 2026 when SCN 26-62 superseded it.
- Preceded by an experimental phase / public comment period (PNS 26-08).

---

## Official documentation
- NWS Service Change Notice 26-62 (implementation): https://www.weather.gov/media/notification/pdf_2026/scn26-62_GTGN_v1.0.pdf
- NWS Service Change Notice 26-46 (earlier implementation notice): https://www.weather.gov/media/notification/pdf_2026/scn26-46_gtgn_implementation.pdf
- NWS Public Information Statement 26-08 (experimental GTGN comment period): https://www.weather.gov/media/notification/pdf_2026/pns26-08_ExpGTGN.pdf
- Display: https://aviationweather.gov/gfa/#obs
