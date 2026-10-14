# NSSL MPAS (Experimental)

## What this model is
The NSSL MPAS runs are a set of experimental, real-time, convection-allowing forecasts run by NOAA's National Severe Storms Laboratory using the Model for Prediction Across Scales (MPAS) dynamical core. They are part of a collaborative effort between NSSL, NOAA's Global Systems Laboratory (GSL), and NCAR to develop the MPAS-based configuration intended for the second version of the [Rapid Refresh Forecast System (RRFS)](./rrfs.md). The runs have been distributed as three configurations that share a common codebase and 3 km CONUS domain but differ in initialization source and microphysics, which allows direct comparison of physics choices and of MPAS against the current operational convection-allowing suite.

**Two of the three configurations are publishing as of 2026-10-14: the HRRR-initialized `mpasht2` and the RRFS-initialized 2-moment `mpasrn`.** `mpasrn` went through a roughly six-week publication gap (no cycles between 2026-08-12 00 UTC and 2026-09-25 00 UTC) and has since resumed. The RRFS-initialized 3-moment `mpasrn3` has not published since its 2026-03-16 00 UTC cycle; its earlier cycles remain archived under the `2026/` catalog directory. The real-time cross-configuration comparison is therefore available again for HRRR- vs RRFS-initialized TEMPO/NSSL 2-moment runs, but not for the 3-moment scheme.

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
- **Forecast length:** 48 h for `mpasht2` (hourly output; f00–f48, 49 steps per cycle). **`mpasrn` likely runs longer — TBD.** Its per-cycle tar archives were consistently ~1.73× the size of `mpasht2`'s (8.48–8.72 GB against 4.91–5.02 GB across 2026-08-02 → 2026-08-07). At comparable per-step size that implies ≈ 85 steps, i.e. f00–f84, which would match the 84 h length used by the other RRFS-initialized CAMs in this suite and is consistent with `mpasrn`'s doubled publication latency. The competing explanation is a larger per-step field set from the NSSL 2-moment microphysics, though the distributed product is a curated subset that is likely common across configurations. Resolve by listing the contents of one `mpasrn` tar. Since the 2026-09-25 resumption, `mpasrn` archives have been around or slightly over 8 GB. That is a little below the August range but still ≈ 1.6× `mpasht2`, so there is no sign of a change in forecast length.
- **Update frequency / cycles:** 4× daily (00/06/12/18 UTC) for `mpasht2`. 2× daily (00/12 UTC) for `mpasrn`, both before its gap and since resuming. The inactive `mpasrn3` ran 2× daily.
- **Temporal output resolution:** Hourly (one GRIB2 file per forecast hour)

### Configurations

| Configuration | File tag | Initialized from | Microphysics | Cycles | Publication lag | Status |
|---|---|---|---|---|---|---|
| MPAS-HTPO-NSSL | `mpasht2` | Operational [HRRR](./hrrr.md) | TEMPO (Thompson-Eidhammer for Operations) | 4× daily (00/06/12/18 UTC) | ~T+8 h 10 m | **Active** (as of 2026-09-10 check) |
| MPAS-RN-NSSL | `mpasrn` | Experimental RRFS (EMC); post-resumption source TBD | NSSL 2-moment | 2× daily (00/12 UTC) | ~T+16 h 15 m (Aug 2026); T+15 h 09 m – T+18 h 47 m in two post-resumption samples | **Active** — resumed 2026-09-25 00 UTC after a gap from 2026-08-12 12 UTC; newest cycle 2026-10-13 00 UTC (as of 2026-10-14) |
| MPAS-RN3-NSSL | `mpasrn3` | Experimental RRFS (EMC) | NSSL 3-moment | 2× daily (00/12 UTC) | — | Discontinued — last cycle 2026-03-16 00 UTC |

**On `mpasrn`'s August–September 2026 gap (flag).** No `mpasrn` archives were published after the **2026-08-12 00 UTC** cycle (`https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/2026/26081200_mpasrn.tar`) until the **2026-09-25 00 UTC** cycle (`https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/2026/26092500_mpasrn.tar`), a gap of about six weeks. Publication has continued since; the newest cycle as of 2026-10-14 is `https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/2026/26101300_mpasrn.tar`. Both 00 and 12 UTC cycles have returned. Whether any post-resumption cycles were empty stubs has not yet been checked — TBD. No explanation from NSSL for the gap or the restart has been located.

The start of the gap lines up with the RRFS pre-implementation cutover documented in the [RRFS entry](./rrfs.md). On 2026-08-12 the `s3://noaa-rrfs-pds` prototype bucket froze after its 11 UTC cycle, and the parallel feed moved to NOMADS `rrfs/para/` from the 12 UTC cycle. The 00 UTC `mpasrn` cycle was the last one whose parent RRFS run existed in the prototype stream, and the 12 UTC cycle, the first missing, would have needed RRFS output that was only on NOMADS. Loss of its initial-condition source therefore remains the most likely cause of the gap, and the resumption is consistent with NSSL repointing `mpasrn` at the NOMADS parallel feed. Neither point is confirmed, and the run's current initial-condition source is TBD. Because the parent RRFS is itself due to change at implementation (see the [RRFS entry](./rrfs.md)), `mpasrn` may be disrupted again around that cutover.

This entry has twice recorded `mpasrn` as discontinued in error. An earlier revision put the stop after 2026-06-16, but the configuration was publishing normally across 2026-08-02 → 2026-08-07 (e.g. `26080700_mpasrn.tar`, 8.721 GB, posted 2026-08-07T16:15:16Z). The 2026-09-10 revision then recorded it as discontinued after 2026-08-12, based on the four-week gap visible at the time; it resumed two weeks later. Whether there was also a genuine hiatus between mid-June and early August remains unestablished.

**On `mpasrn3`'s discontinuation (flag).** The last published `mpasrn3` archive is the **2026-03-16 00 UTC** cycle (`https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/2026/26031600_mpasrn3.tar`); nothing newer appeared in the `2026/` catalog listing through 2026-09-10. That is close to six months, during which it did not resume following NSSL's Jet→Ursa HPC transition. No retirement notice from NSSL has been located, and the stale CAMs run descriptions still present it as a current run (see *Notes*). Given `mpasrn`'s six-week gap and restart, a return of `mpasrn3` cannot be ruled out either.

The cause is TBD, but it was not the RRFS input change behind `mpasrn`'s gap. Both configurations were initialized from the same experimental EMC RRFS, and `mpasrn` kept running from that source for another five months. The stop falls about two weeks before Jet's decommissioning (~31 March 2026), so a cut in runs ahead of the HPC move is plausible, but unconfirmed.

---

## Data assimilation
- **Data assimilation:** No — the NSSL MPAS runs are cold-start. They do not perform their own analysis.

---

## Initial and boundary conditions
- **Initial conditions:** Parent-model fields — operational HRRR (MPAS-HTPO, active) or the experimental deterministic RRFS provided by EMC (MPAS-RN, active; MPAS-RN3, discontinued). Which RRFS stream MPAS-RN has drawn on since its 2026-09-25 resumption is TBD (the prototype S3 bucket it originally used froze on 2026-08-12).
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
- **Typical archive size:** ~4.9–5.0 GB per `mpasht2` cycle; ~8.5–8.7 GB per `mpasrn` cycle in August 2026, and around or slightly over 8 GB since the 2026-09-25 resumption. There is no per-forecast-hour access path — the tar is the unit of distribution, so retrieving a single forecast hour requires downloading the whole cycle.
- **Official download location:**
  - Browse (THREDDS catalog): https://data.nssl.noaa.gov/thredds/catalog/customConfig/FRDD/NSSL-MPAS.html
  - Year directory (per-cycle file listing): https://data.nssl.noaa.gov/thredds/catalog/FRDD/NSSL-MPAS/2026/catalog.html
  - Direct download (HTTPServer): `https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/<YYYY>/<YYMMDDHH>_<tag>.tar`
    - e.g., `https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/2026/26091012_mpasht2.tar`
    - e.g., `https://data.nssl.noaa.gov/thredds/fileServer/FRDD/NSSL-MPAS/2026/26101300_mpasrn.tar`
  - Each `.tar` contains files named `<tag>_nssl_YYYYMMDDHHfFF.grib2.gz`
- **Metadata:** ISO 19115 record available per dataset via the THREDDS `iso/` service

### Publication cadence and failure mode

Publication lag is long and highly regular. Across 2026-08-02 → 2026-08-07, `mpasht2` archives appeared at T + 8 h 10 m (±2 min over 17 cycles), and `mpasrn` posted at T + 16 h 15 m (±1 min over 8 cycles). Neither stream was usable for real-time short-range application; `mpasrn` in particular completed publication well after its own forecast period had substantially elapsed. Since the 2026-09-25 resumption, `mpasrn`'s lag appears less regular. The first cycle back (`26092500_mpasrn.tar`) posted at 2026-09-25T18:46:48Z (T + 18 h 47 m), possibly affected by the restart itself. `26101300_mpasrn.tar` posted at 2026-10-13T15:08:40Z (T + 15 h 09 m). Two samples are not enough to re-establish a typical lag — TBD.

Note that archive publication lags the rendered imagery on the NSSL CAMs viewer by several hours — the CAMs availability times are not a guide to when the gridded data lands on PARR.

**Failed cycles are published as empty archives rather than omitted.** When a run does not deliver, the scheduler writes a valid but empty tar of exactly **10,240 bytes** (the canonical empty-GNU-tar size, 20 × 512-byte blocks) at the nominal deadline. These are distinguishable from real archives in two ways:

- **Size:** 10.24 KB against ~5 GB (`mpasht2`) / ~8.6 GB (`mpasrn`)
- **Timestamp:** written on the deadline to the second (e.g. `14:00:02`, `20:00:02`, `04:00:01`), whereas successful archives post ~10–15 minutes past it

This matters for automated retrieval: the request returns HTTP 200 and a structurally valid tar, so neither the status code nor an archive-integrity check will flag the failure. Clients should assert a minimum file size. Five such stubs occurred in the six-day sample — `mpasht2` at 2026-08-04 06/12/18 UTC (three consecutive cycles) and `mpasrn` at 2026-08-04 12 UTC and 2026-08-05 12 UTC — so the failure rate is not negligible.

Separately from stubs, a configuration can stop publishing entirely for weeks and then return: `mpasrn` published nothing for about six weeks (2026-08-12 12 UTC → 2026-09-24) before resuming. A multi-week gap is therefore not by itself evidence that a configuration has been retired. Clients should tolerate extended absences, and status labels in this entry should be read as "last observed" rather than final.

---

## Recent version history
- **2026-09-25 — `mpasrn` (MPAS-RN-NSSL) resumes.** First cycle after the gap is 00 UTC; publishing continues 2× daily (00/12 UTC), newest 2026-10-13 00 UTC as of 2026-10-14. Archives are ~8 GB. Current initial-condition source TBD (see *Configurations*).
- **2026-08-12 — `mpasrn` publication gap begins.** Last cycle before the gap is 00 UTC; the 12 UTC cycle, the first missing, was the first RRFS cycle published only on NOMADS after the prototype-bucket freeze. Causal link unconfirmed (see *Configurations*).
- **~2026-03-31 — NOAA Jet HPC decommissioned.** NSSL runs moved to Ursa; the legacy 4 km WRF-NSSL run was scheduled to cease at the same time.
- **2026-03-16 — `mpasrn3` (MPAS-RN3-NSSL) discontinued.** Last cycle 00 UTC. Did not resume after the HPC transition; cause unconfirmed (see *Configurations*).
- **Fall 2024 — MPAS runs begin at GSL.** The NSSL runs now use GSL's MPAS codebase; the date of that switch is not documented.
- **January 2023 — MPAS runs begin at NSSL.**

---

## Notes
- **Related GSL run (not separately cataloged):** GSL runs a companion MPAS configuration, **MPAS-RRFSA-GSL** (initialized from RRFS-A, TEMPO microphysics), plus reduced/higher-resolution variants (3.5 km MPASLR-RRFSA-GSL, 1.25 km). These are displayed on the NSSL CAMs viewer and Pivotal Weather but are **not** published to PARR and have no open gridded feed found, so they are excluded under the repository's downloadable-data requirement.
- **Display/viewer:** Rendered imagery for all of these runs is viewable at the NSSL CAMs site (https://cams.nssl.noaa.gov/) and via Pivotal Weather; those are visualization front-ends, not the data feed.
- **CAMs MPAS imagery outage (from 28 August 2026).** A notice on the CAMs site dated 28 August 2026 reports that the machine handling transfer of the MPAS runs from NOAA RDHPCS failed, leaving MPAS graphics unavailable until a replacement machine is in place; HRRR and RRFS-EMC graphics were unaffected. `mpasht2` gridded archives were still publishing on PARR as of 2026-09-10, and `mpasrn` archives have been publishing again since 2026-09-25, so missing CAMs imagery is not evidence that a run has stopped. Whether the imagery outage has been resolved is TBD.
- **The CAMs run-schedule documentation is stale.** The schedule and configuration text on the CAMs site self-dates to 4 September 2025. It still describes MPAS-RN3-NSSL as a current run and states that the runs execute on the NOAA Jet HPC system, both of which are superseded (Jet was decommissioned around 31 March 2026). Its description of MPAS-RN-NSSL predates the 2026 gap and any change of initial-condition source. Its availability times refer to imagery, not to PARR archive publication. Prefer the live catalog over this page.
- **Relationship to RRFS:** This is the MPAS development line for [RRFSv2](./rrfs.md). The operational [RRFSv1](./rrfs.md) and its ensemble [REFS](../../../ensemble_models/regional/usa/refs.md) use the FV3-LAM core; RRFSv2 is planned to transition to MPAS.

---

## Status
- Experimental / pre-operational. GSL states these systems are not for operational use and the supporting websites are not maintained 24/7.
- **Two configurations are active as of 2026-10-14:** `mpasht2` (4× daily; newest cycle checked `26091012_mpasht2.tar` on 2026-09-10) and `mpasrn` (resumed 2026-09-25 00 UTC after a ~six-week gap; newest cycle `26101300_mpasrn.tar`). `mpasrn3` has not published since its 2026-03-16 00 UTC cycle; see the flags under *Configurations*.
- NSSL's legacy 4 km WRF-NSSL run was scheduled to cease on or near 31 March 2026 (Jet decommissioning) as resources shifted toward MPAS development.
- Given the empty-archive failure mode, the long publication lag, and `mpasrn`'s six-week gap and return, status should be judged from a multi-week listing and treated as provisional even then.
- Expected to feed the eventual operational RRFSv2; once RRFSv2 is implemented and lands on NOMADS/AWS, a separate operational entry will be warranted.

---

## Official documentation
- PARR (NSSL Data Repository) THREDDS root: https://data.nssl.noaa.gov/thredds/catalog/catalog.html
- NSSL-MPAS catalog: https://data.nssl.noaa.gov/thredds/catalog/customConfig/FRDD/NSSL-MPAS.html
- NSSL CAMs (viewer + run descriptions; schedule text stale as of Sep 2025): https://cams.nssl.noaa.gov/
- GSL Predictions / RRFSv2 development: https://gsl.noaa.gov/research/predictions/
- MPAS model home (NCAR): https://mpas-dev.github.io/
- RRFSv2 dynamical-core background — Carley, J., et al. (2023), NOAA/EMC Office Note 516: https://doi.org/10.25923/ccgj-7140
- NSSL Forecast webpage: https://www.nssl.noaa.gov/tools/forecast/
- Data contact / publisher: DOC/NOAA/OAR/NSSL — nssl.support@noaa.gov
- MPAS-NSSL scientific contact: adam.clark@noaa.gov
