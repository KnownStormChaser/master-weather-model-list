# NSSL MPAS (Experimental)

## What this model is
The NSSL MPAS runs are a set of experimental, real-time, convection-allowing forecasts run by NOAA's National Severe Storms Laboratory using the Model for Prediction Across Scales (MPAS) dynamical core. They are part of a collaborative effort between NSSL, NOAA's Global Systems Laboratory (GSL), and NCAR to develop the MPAS-based configuration intended for the second version of the [Rapid Refresh Forecast System (RRFS)](./rrfs.md). The runs are distributed as three configurations that share a common codebase and 3 km CONUS domain but differ in initialization source and microphysics, allowing direct comparison of physics choices and of MPAS against the current operational convection-allowing suite.

Two of the three configurations were publishing as of 2026-08-07: the HRRR-initialized `mpasht2` and the RRFS-initialized 2-moment `mpasrn`. The 3-moment `mpasrn3` has not been observed since 2026-03-16.

These are experimental, pre-operational research runs — not an operational NWP product — but they are run on a fixed schedule rather than as one-off case studies, and they represent the development line that is expected to become RRFSv2.

---

## Who runs it
- **Organization:** NOAA / National Severe Storms Laboratory (NSSL), Forecast Research and Development Division (FRDD); developed collaboratively with NOAA/GSL and NCAR
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Contiguous United States (CONUS)
- **Domain details:** Distributed on the HRRR 3-km CONUS Lambert-conformal grid (1799 × 1059), spanning roughly 21.1–52.6°N and 234–299°E (≈ −126° to −61°W). The model itself runs on an MPAS unstructured Voronoi mesh; distributed files are regridded to the Lambert grid.

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing), experimental
- **Model system / core:** MPAS (Model for Prediction Across Scales), atmospheric component, with a subset of Advanced Research WRF (ARW) physics
- **Dynamical formulation:** Non-hydrostatic, finite-volume on an unstructured Voronoi mesh
- **Convection-allowing:** Yes — 3 km grid spacing; no cumulus parameterization
- **Horizontal resolution:** 3 km. Distributed output is on the operational HRRR CONUS Lambert-conformal grid (1799 × 1059; Dx = Dy = 3 km; standard parallels 38.5°N, central longitude 262.5°E / −97.5°W; spherical earth R = 6,371,229 m). The native MPAS unstructured Voronoi mesh is regridded to this grid for distribution.
- **Vertical levels:** Distributed output is provided on 10 isobaric levels (250, 500, 700, 750, 800, 850, 900, 925, 950, 1000 hPa), plus surface/near-surface and layer diagnostics. The native MPAS model level count is not exposed in the distributed GRIB2.
- **Forecast length:** 48 h for `mpasht2` (hourly output; f00–f48, 49 steps per cycle). **`mpasrn` may run longer — unverified.** Its per-cycle tar archives are consistently ~1.73× the size of `mpasht2`'s (8.48–8.72 GB against 4.91–5.02 GB across 2026-08-02 → 2026-08-07). At comparable per-step size that implies ≈ 85 steps, i.e. f00–f84, which would match the 84 h length used by the other RRFS-initialized CAMs in this suite and is consistent with `mpasrn`'s doubled publication latency. The competing explanation is a larger per-step field set from the NSSL 2-moment microphysics, though the distributed product is a curated subset that is likely common across configurations. Resolve by listing the contents of one `mpasrn` tar.
- **Update frequency / cycles:** Varies by configuration (see table below)
- **Temporal output resolution:** Hourly (one GRIB2 file per forecast hour)

### Configurations

| Configuration | File tag | Initialized from | Microphysics | Cycles | Publication lag | Status |
|---|---|---|---|---|---|---|
| MPAS-HTPO-NSSL | `mpasht2` | Operational [HRRR](./hrrr.md) | TEMPO (Thompson-Eidhammer for Operations) | 4× daily (00/06/12/18 UTC) | ~T+8 h 10 m | Active |
| MPAS-RN-NSSL | `mpasrn` | Experimental RRFS (EMC) | NSSL 2-moment | 2× daily (00/12 UTC) | ~T+16 h 15 m | Active |
| MPAS-RN3-NSSL | `mpasrn3` | Experimental RRFS (EMC) | NSSL 3-moment | 2× daily (00/12 UTC) | — | Not observed since 2026-03-16 |

**On `mpasrn`'s status (flag).** An earlier revision of this entry recorded `mpasrn` as discontinued after 2026-06-16. That is incorrect as a present-tense statement — the configuration was publishing normally on 2026-08-07 (`26080700_mpasrn.tar`, 8.721 GB, posted 2026-08-07T16:15:16Z), on its documented 00/12 UTC cadence. What is **not** established is whether there was a genuine hiatus between mid-June and some resumption date, or whether the earlier reading was an artefact of the long publication lag and the empty-archive failure mode described below. Confirming this requires a full listing of the `2026/` catalog directory; the evidence to hand covers only 2026-08-02 → 2026-08-07.

**On `mpasrn3` (flag).** No `mpasrn3` archives appear anywhere in the 2026-08-02 → 2026-08-07 window, supporting continued inactivity, but this is absence over a six-day window rather than a positive confirmation of retirement. Treat "discontinued" as provisional pending a full-year listing or confirmation from NSSL.

---

## Data assimilation
- **Data assimilation:** No — the NSSL MPAS runs are cold-start. They do not perform their own analysis.

---

## Initial and boundary conditions
- **Initial conditions:** Parent-model fields — operational HRRR (MPAS-HTPO) or the experimental deterministic RRFS provided by EMC (MPAS-RN / MPAS-RN3)
- **Boundary conditions:** TBD (inherited from the respective parent system)

---

## What it provides
Deterministic convection-allowing output in GRIB2, as a compact curated field set (72 messages per forecast hour in the sampled f00 file). Contents:

- **Isobaric suite (10 levels: 250–1000 hPa):** geopotential height, temperature, specific humidity, u/v wind, geometric vertical velocity
- **Convective / radar diagnostics:** composite reflectivity, derived reflectivity (1 km AGL and −10 °C isotherm), hourly-maximum reflectivity (1 km AGL), echo top, maximum updraft and downdraft vertical velocity
- **Other:** mean sea level pressure, visibility, surface wind gust

Note: this set does **not** include standard 2 m / 10 m surface fields (2 m temperature/dewpoint, 10 m winds) or accumulated precipitation — it is an upper-air-plus-convective-diagnostics subset rather than a full sensible-weather field list.

---

## Data availability
- **Is the data free?** Yes
- **License:** "Freely available" — the rights statement carried in the dataset's own THREDDS/ISO 19115 metadata (publisher: DOC/NOAA/OAR/NSSL). As a U.S. Government work it is effectively public domain (CC0-equivalent). Note: the CC BY 4.0 banner in the THREDDS server's page header is generic boilerplate describing the NSSL ATD radar archive and does **not** apply at the MPAS dataset level.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (gzipped per-forecast-hour files bundled in per-cycle `.tar` archives)
- **Typical archive size:** ~4.9–5.0 GB per `mpasht2` cycle; ~8.5–8.7 GB per `mpasrn` cycle. There is no per-forecast-hour access path — the tar is the unit of distribution, so retrieving a single forecast hour requires downloading the whole cycle.
- **Official download location:**
  - Browse (THREDDS catalog): https://data.nssl.noaa.gov/thredds/catalog/FRDD/NSSL-MPAS/catalog.html
  - Direct download (HTTPServer): `https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/<YYYY>/<YYMMDDHH>_<tag>.tar`
    - e.g., `.../2026/26080700_mpasrn.tar`
  - Each `.tar` contains files named `<tag>_nssl_YYYYMMDDHHfFF.grib2.gz`
- **Metadata:** ISO 19115 record available per dataset via the THREDDS `iso/` service

### Publication cadence and failure mode

Publication lag is long, highly regular, and configuration-dependent. Across 2026-08-02 → 2026-08-07, `mpasht2` archives appeared at T + 8 h 10 m (±2 min over 17 cycles) and `mpasrn` archives at T + 16 h 15 m (±1 min over 8 cycles). Neither stream is usable for real-time short-range application; `mpasrn` in particular completes publication well after its own forecast period has substantially elapsed.

Note that archive publication lags the rendered imagery on the NSSL CAMs viewer by several hours — the CAMs availability times are not a guide to when the gridded data lands on PARR.

**Failed cycles are published as empty archives rather than omitted.** When a run does not deliver, the scheduler writes a valid but empty tar of exactly **10,240 bytes** (the canonical empty-GNU-tar size, 20 × 512-byte blocks) at the nominal deadline. These are distinguishable from real archives in two ways:

- **Size:** 10.24 KB against ~5 GB / ~8.6 GB
- **Timestamp:** written on the deadline to the second (e.g. `14:00:02`, `20:00:02`, `04:00:01`), whereas successful archives post ~10–15 minutes past it

This matters for automated retrieval: the request returns HTTP 200 and a structurally valid tar, so neither the status code nor an archive-integrity check will flag the failure. Clients should assert a minimum file size. Five such stubs occurred in the six-day sample — `mpasht2` at 2026-08-04 06/12/18 UTC (three consecutive cycles) and `mpasrn` at 2026-08-04 12 UTC and 2026-08-05 12 UTC — so the failure rate is not negligible, and an extended run of stubs is a plausible way for an active configuration to appear retired.

---

## Notes
- **Related GSL run (not separately cataloged):** GSL runs a companion MPAS configuration, **MPAS-RRFSA-GSL** (initialized from RRFS-A, TEMPO microphysics), plus reduced/higher-resolution variants (3.5 km MPASLR-RRFSA-GSL, 1.25 km). These are displayed on the NSSL CAMs viewer and Pivotal Weather but are **not** published to PARR and have no open gridded feed found, so they are excluded under the repository's downloadable-data requirement.
- **Display/viewer:** Rendered imagery for all of these runs is viewable at the NSSL CAMs site (https://cams.nssl.noaa.gov/) and via Pivotal Weather; those are visualization front-ends, not the data feed.
- **The CAMs run-schedule documentation is stale.** The schedule and configuration text on the CAMs site self-dates to 4 September 2025. It still lists MPAS-RN3-NSSL as an active run and states that the runs execute on the NOAA Jet HPC system, both of which are superseded (Jet was decommissioned around 31 March 2026). Its availability times refer to imagery, not to PARR archive publication. Prefer the live catalog over this page.
- **Relationship to RRFS:** This is the MPAS development line for [RRFSv2](./rrfs.md). The operational [RRFSv1](./rrfs.md) and its ensemble [REFS](../../../ensemble_models/regional/usa/refs.md) use the FV3-LAM core; RRFSv2 is planned to transition to MPAS.
- **MPAS run history:** MPAS runs began at NSSL in early January 2023 and at GSL in fall 2024. The NSSL runs now use GSL's MPAS codebase.

---

## Status
- Experimental / pre-operational. GSL states these systems are not for operational use and the supporting websites are not maintained 24/7.
- **`mpasht2` and `mpasrn` were both publishing as of 2026-08-07**, on 4×-daily and 2×-daily cadences respectively. `mpasrn3` has not been observed since 2026-03-16 and did not resume following NSSL's Jet→Ursa HPC transition; see the flags under *Configurations* for the limits of that evidence.
- NSSL's legacy 4 km WRF-NSSL run was scheduled to cease on or near 31 March 2026 (Jet decommissioning) as resources shifted toward MPAS development.
- Given the empty-archive failure mode and the long publication lag, a configuration's status should be judged from a multi-week listing rather than a single check of the most recent cycles.
- Expected to feed the eventual operational RRFSv2; once RRFSv2 is implemented and lands on NOMADS/AWS, a separate operational entry will be warranted.

---

## Official documentation
- PARR (NSSL Data Repository) THREDDS root: https://data.nssl.noaa.gov/thredds/catalog/catalog.html
- NSSL-MPAS catalog: https://data.nssl.noaa.gov/thredds/catalog/FRDD/NSSL-MPAS/catalog.html
- NSSL CAMs (viewer + run descriptions; schedule text stale as of Sep 2025): https://cams.nssl.noaa.gov/
- GSL Predictions / RRFSv2 development: https://gsl.noaa.gov/research/predictions/
- MPAS model home (NCAR): https://mpas-dev.github.io/
- RRFSv2 dynamical-core background — Carley, J., et al. (2023), NOAA/EMC Office Note 516: https://doi.org/10.25923/ccgj-7140
- NSSL Forecast webpage: https://www.nssl.noaa.gov/tools/forecast/
- Data contact / publisher: DOC/NOAA/OAR/NSSL — nssl.support@noaa.gov
- MPAS-NSSL scientific contact: adam.clark@noaa.gov
