# HMON (Hurricanes in a Multi-scale Ocean-coupled Non-hydrostatic Model)

## What this model is
HMON (Hurricanes in a Multi-scale Ocean-coupled Non-hydrostatic Model) is a specialized tropical cyclone numerical weather prediction system developed by NOAA to forecast hurricane track, intensity, structure, and rainfall. It is a storm-following, telescopic-nest model built on the NMMB dynamical core, coupled to the HYCOM ocean model.

HMON was first implemented operationally on August 15, 2017 (v1.0.0) as the replacement for the legacy GFDL Hurricane Model (GHM), and ran operationally alongside [HWRF](hwrf.md) as NOAA's dual-deterministic hurricane pair. With the 2023 transition to the FV3-based [HAFS](hafs.md), HMON moved to **legacy status** — frozen at its final pre-HAFS configuration with no further scientific upgrades — but it is **still run operationally for active tropical cyclones** as guidance alongside HAFS. As of mid-2026 no retirement date has been announced, and HMON cycles are being produced in real time (live-verified July 2026).

---

## Who runs it
- **Organization:** NOAA / NWS / NCEP — Environmental Modeling Center (EMC, development) and NCEP Central Operations (NCO, operations)
- **Country / region:** United States

---

## When this model runs
HMON runs **on demand for active tropical cyclones**, triggered by storm messages from the National Hurricane Center (NHC). Each run is centered on a specific storm rather than a fixed geographic domain.

- **Cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Capacity:** up to **5 storms per cycle**

---

## What area it covers
- **Coverage:** Storm-centered, storm-following domains in NHC's basins only — **North Atlantic (NATL), Eastern Pacific (EPAC), and Central Pacific (CPAC)**. Unlike [HWRF](hwrf.md), HMON does not run global/JTWC basins. *Live-verified July 2026: HMON cycles carried only Eastern Pacific (and, by design, Atlantic) storms — no Western Pacific storm appeared.*
- **Storm selection:** Driven by official warning/invest messages from NHC. Invest areas use `9x`-series IDs (e.g. `invest98e`).

---

## Basic details
- **Model type:** Tropical cyclone NWP (deterministic), atmosphere–ocean coupled
- **Model core:** NMMB (Non-hydrostatic Multi-scale Model on a B grid)
- **Native model grid:** three storm-centric nested domains (one parent + two moving nests) at nominal **18 km / 6 km / 2 km**. The outermost domain is ~80° × 80°, approximately centered on the storm; the two inner nests are smaller and follow the storm through the integration.
- **Distributed output grids (all `regular_ll`; live-verified 2026-07-25):**

  | Output domain | Resolution | Grid (Ni × Nj) | Extent |
  |---|---|---|---|
  | `d1` | 0.20° (~22 km) | 551 × 451 | ~110° × 90°, storm-centered parent |
  | `d2` | 0.06° (~6.5 km) | 234 × 201 | ~14° × 12°, intermediate nest |
  | `d3` | 0.02° (~2 km) | 450 × 375 | ~9° × 7.5°, inner nest |

- **Vertical levels:** distributed output on **45 pressure levels** (1000–2 hPa, verified). Native model: **71 vertical levels, model top 50 hPa** (per NOAA's current HMON model description; earlier versions used fewer — 43 at v1.0.0 — and published counts vary across sources, so treat the native figure as documented rather than independently verified).
- **Forecast length:** 126 hours
- **Update frequency / cycles:** 4× daily
- **Temporal output resolution:** 3-hourly (f000–f126; live-verified)

---

## Initialization and data assimilation
- **Vortex initialization:** vortex relocation
- **Data assimilation:** **none** — HMON has no data assimilation system. This is a key difference from [HWRF](hwrf.md) (which uses HDAS) and [HAFS](hafs.md) (inner-core DA).
- **Initial and boundary conditions:** provided by the NCEP Global Forecast System (GFS)

---

## Ocean / wave coupling
- **Ocean coupling:** two-way coupling to the **HYCOM** ocean model for **North Atlantic, Eastern Pacific, and Central Pacific** basins. *(Note: at first implementation in 2017, HMON ran the North Atlantic uncoupled and coupled only EPAC/CPAC; NOAA's current model description indicates all three NHC basins are now HYCOM-coupled.)*
- **Wave coupling:** none. HMON distributes no wave products.

---

## What it provides
Per storm, per cycle, the public NOMADS tree distributes (live-verified 2026-07-25). File naming: `{stormname}{NN}{basin}.{YYYYMMDDHH}.{product}` — e.g. `fausto06e.2026072512.hmonprs.d3.0p02.f000.grb2`.

**Gridded GRIB2 forecast fields** (3-hourly, f000–f126, each with a `.idx` sidecar):
- `hmonprs.d1.0p20.f###.grb2` — parent-domain pressure-level and surface fields (~789 messages, 45 pressure levels: winds, temperature, RH, geopotential, MSLP, precipitation, surface/flux fields)
- `hmonprs.d2.0p06.f###.grb2` — intermediate nest
- `hmonprs.d3.0p02.f###.grb2` — inner nest (~2 km)

**Track, bulletin, and diagnostic products:**
- `trak.hmon.atcfunix` — **ATCF track and intensity guidance** (standard operational format)
- `afos` — AFOS-format text bulletin
- `precip.asci`, `sfcwind.asci` — plain-text precipitation and surface-wind summaries
- `stats.tpc`, `grib.stats.short` — verification / diagnostic statistics

HMON is the leanest of NOAA's three storm-following hurricane systems in terms of distributed products: **it produces no synthetic-satellite (`sat`) stream, no swath summary, and no wave output** — only the three gridded pressure-level domains plus track and text products.

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work). Distributed via NOAA Open Data Dissemination (NODD): open to public use; NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (three domains, each with `.idx`), ATCF track text (`trak.hmon.atcfunix`), AFOS bulletin, plain-text ASCII summaries, and diagnostic stats
- **Official download location (NOMADS only — no AWS Open Data mirror):**
  - https://nomads.ncep.noaa.gov/pub/data/nccf/com/hmon/prod/
  - FTP equivalent: ftp://ftp.ncep.noaa.gov/data/nccf/com/hmon/prod/
  - Layout: `hmon.YYYYMMDD/{00,06,12,18}/…`, with the storm ID as a filename prefix (no per-storm subdirectories). Real-time retention on NOMADS is short (roughly the two most recent days); there is no separate input-staging directory in `prod`.

---

## Notes
- **HMON is legacy but still operational.** It was superseded as part of NOAA's primary hurricane suite by [HAFS](hafs.md) in 2023 and is frozen at its final configuration, but it continues to run for active storms and remains a reference/consensus model. No retirement date has been announced as of mid-2026.
- HMON uses storm-following, two-way interactive moving nests rather than a fixed domain.
- HMON has **no data assimilation** — it relies on vortex relocation plus GFS initial/boundary conditions. This makes it computationally lighter but distinct in design from HWRF and HAFS.
- Product availability depends on tropical cyclone activity in NATL/EPAC/CPAC — no active storms in those basins means no data.

---

## Relationship to other models
- **Predecessor:** HMON replaced the discontinued **GFDL Hurricane Model (GHM)** in operations in 2017.
- **[HWRF](hwrf.md):** Ran operationally alongside HMON as NOAA's dual-deterministic hurricane pair; also now legacy but still produced. HWRF is the more sophisticated system (with data assimilation and global basin coverage); HMON is lighter and NHC-basin-only.
- **[HAFS](hafs.md):** Successor and current NOAA primary hurricane system (operational 2023). HMON now runs as legacy guidance alongside it. The modern successor chain is GFDL → HWRF/HMON → HAFS.

---

## Official documentation
- HMON text description (NOMADS): https://nomads.ncep.noaa.gov/txt_descriptions/HMON_doc.shtml
- EMC legacy HMON page: https://www.emc.ncep.noaa.gov/gc_wmb/vxt/HMON_legacy/about.php?branch=mdls
- AOML Hurricane Model Viewer — model descriptions: https://storm.aoml.noaa.gov/viewer/model_description.html
- HMON v1.0.0 implementation notice (SCN 17-83, 2017): https://weather.gov/media/notification/pdfs/scn17-83hmon_aaa.pdf
- NCEP hurricane data products: https://www.nco.ncep.noaa.gov/pmb/products/hur/
