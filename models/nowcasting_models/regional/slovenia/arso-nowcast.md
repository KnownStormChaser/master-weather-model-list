# ARSO Nowcast (Slovenia)

## What this model is
ARSO Nowcast is the Slovenian Environment Agency's gridded weather-analysis and very-short-range forecast (nowcast) product covering Slovenia and the surrounding region. It provides 1 km fields of near-surface air temperature, near-surface wind, precipitation, and sky openness (clear-sky fraction), refreshing every 30 minutes to one hour. The distributed GRIB files are named `public_inca_*`, identifying the product as ARSO's implementation of **INCA** (Integrated Nowcasting through Comprehensive Analysis) — the Slovenian node of the INCA-CE Central European family. See the [INCA (Austria)](../austria/inca.md) entry for the method family (a seamless extrapolation–NWP blend built on a comprehensive multi-source analysis). ARSO's own cited documentation does not describe the method or configuration, so ARSO-specific details (NWP first guess, blending timescales, input sources) remain flagged below.

---

## Who runs it
- **Organization:** Slovenian Environment Agency (ARSO)
- **Country / region:** Slovenia

---

## What area it covers
- **Coverage:** Slovenia and the immediately surrounding region (the domain extends beyond Slovenia into the North Adriatic, NE Italy, Istria/Croatia, southern Austria, and western Hungary)
- **Domain details:** Lambert Conformal Conic projection, 401 × 301 grid at 1 km spacing (120,701 points; ~401 × 301 km). Standard parallel 46.12°N, central meridian 14.815°E, spherical Earth. SW (first) grid point ≈ 44.688°N, 12.235°E. (Read directly from the distributed GRIB1 grid definition.)

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** INCA — a seamless extrapolation–NWP blend built on a comprehensive multi-source analysis (identified from the `public_inca_*` file naming; see the [INCA (Austria)](../austria/inca.md) entry for the method family). ARSO's cited documentation does not itself describe the method, so the family attribution rests on the file naming (flag for confirmation).
- **Technique / algorithm:** Per the INCA family: Lagrangian persistence (advection by motion vectors) for precipitation and cloudiness; NWP-trend-on-analysis (Eulerian) for temperature and wind. ARSO's specific configuration is not documented in the cited sources (TBD).
- **Underlying / driving model:** Not stated in ARSO's cited documentation. INCA systems use the operating centre's NWP as first guess/trend, so ARSO's INCA plausibly draws on [ALADIN-SI](../../../nwp_models/regional/slovenia/aladin-slovenia.md) or ARSO's ALARO-RUC (NWCRUC); which one is not confirmed (flag). This 1 km INCA product is distinct from the 1.3 km ALARO-RUC nowcasting system.
- **Probabilistic / ensemble:** No — single deterministic gridded fields; no probabilistic products documented
- **Horizontal resolution:** 1 km
- **Vertical structure:** 2D single-level surface fields (near-surface temperature and wind, precipitation, sky openness)
- **Lead time:**
  - Precipitation and sky openness: **+0 to +1 h** (3 steps at +0/+30/+60 min, including the +0 analysis)
  - Temperature and wind: **+0 to +6 h** (7 hourly steps, including the +0 analysis)
- **Update frequency:**
  - Precipitation and sky openness: every **30 min**
  - Temperature and wind: every **1 h**
- **Temporal output resolution:** 30-min steps (precipitation, sky openness); 60-min steps (temperature, wind). Confirmed from the distributed GRIB step ranges.
- **Latency:** ~15 min after nominal time (observed from directory timestamps — e.g. the file valid 13:00 appears ~13:15; not formally documented)

---

## Inputs
The observations and/or model fields the nowcast is built from are not specified in the cited ARSO documentation.
- **Radar:** TBD
- **Satellite:** TBD
- **Lightning:** TBD
- **Surface / other observations:** TBD
- **NWP fields:** TBD

---

## What it provides
Gridded 1 km fields of:
- 2 m air temperature (GRIB table 2, param 11; K)
- 10 m horizontal wind components u, v (params 33, 34; m/s)
- total precipitation (param 61; kg m⁻² = mm)
- sky openness — fraction of clear sky, the inverse of cloud cover

Most fields follow the WMO GRIB parameter table; **sky openness is the exception, encoded in local table 219, parameter number 149** (confirmed present in the distributed files).

---

## Data availability
- **Is the data free?** Yes
- **License:** Open with mandatory attribution. ARSO publishes information from meteo.arso.gov.si as freely reusable on the condition that the source is cited as "Vir: Agencija Republike Slovenije za okolje" or "Vir: ARSO" (in English: "Source: Slovenian Environment Agency" or "Source: ARSO"). Mandatory attribution is stipulated by **Article 14 of the Slovenian Act on the State Meteorological, Hydrological, Oceanographic and Seismological Service** (Official Gazette of the Republic of Slovenia, No. 60/17). No specific named open-data licence (e.g., CC BY) is asserted; reuse terms are governed by the ARSO site policy and Slovenian law.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB edition 1, distributed as compressed ZIP archives (one GRIB file per ZIP)
- **File naming:**
  - `nowcast_30min_yyyyMMdd-HHmm.zip` — the 30-min stream (total precipitation, sky openness); extracts to `public_inca_30min_yyyyMMdd-HHmm.grb`
  - `nowcast_yyyyMMdd-HHmm.zip` — the hourly stream (2 m temperature, 10 m wind); extracts to `public_inca_yyyyMMdd-HHmm.grb`
- **Official download location:**
  https://meteo.arso.gov.si/uploads/probase/www/nowcast/data/
- **Retention:** Files are kept for roughly the last **24 hours** (the directory listing spans a rolling 24 h window).

---

## Notes
- The download endpoint carries **two distinct ZIP streams**: a 30-min stream (`nowcast_30min_*.zip`, ~0.1 MB, extracting to a ~0.7 MB `public_inca_30min_*.grb` — total precipitation + sky openness, +0 to +1 h at 30-min steps) and an hourly stream (`nowcast_*.zip`, ~1.2–1.4 MB, extracting to a ~2.5 MB `public_inca_*.grb` — 2 m temperature + 10 m wind, +0 to +6 h hourly). Both share the same 401 × 301, 1 km Lambert Conformal Conic grid.
- **GRIB coding:** parameters follow the WMO GRIB Edition 1 parameter table, with the sole exception of sky openness (local table 219, parameter 149). Decode with `wgrib` or ECMWF's ecCodes.
- **This product is ARSO's INCA** (per the `public_inca_*` file naming), part of the INCA-CE Central European family — see the [INCA (Austria)](../austria/inca.md) entry for the shared method. It is distinct from ARSO's **ALARO-RUC (NWCRUC)** rapid-update NWP nowcasting system (non-hydrostatic 1.3 km, North Adriatic, to +36 h; see [ALADIN Slovenia](../../../nwp_models/regional/slovenia/aladin-slovenia.md)). Which ARSO NWP model supplies INCA's first guess/trend (ALADIN-SI, ALARO-RUC, or another) is not documented in the cited sources — flag for confirmation.
- ARSO publishes the companion 4 km ALADIN-SI NWP feed at a sibling endpoint (`.../www/model/data/`); see the [ALADIN Slovenia](../../../nwp_models/regional/slovenia/aladin-slovenia.md) entry.

---

## Official documentation
- ARSO — GRIB output documentation (weather analysis & very-short-range forecasts):
  https://meteo.arso.gov.si/uploads/meteo/help/sl/NumericniRezultatiGRIB.html
- ARSO website — Conditions of re-use:
  https://meteo.arso.gov.si/met/sl/about/
